# Step 7: Creating the DB in the State Actor

In our last step, we setup the preliminary steps of a brand new project called idea-depot which we derived from the original tutorial-v1 reward branch. Starting from this step, we'll start adding new business logic to turn the tutorial-v1 into idea-depot.

## Building Up from the Data Structure

We can categorize developers based on how their priorities inform their code development. One type builds the front end first so that they can see the UI change. They then use the UI to test the backend or state machines layer. The second type starts from the bottom up. They starts from the the lowest layer, proceeding initially with the state machine layer, then the backend web services layer, and then, at last, the front end. They make sure the under layer works before adding the upper layer to call back to the lower layer. Since I'm the second type of developer who likes to design starting from the data structure, I would approach coding the **idea-depot** the same way. Because there are several developers in a team that can work on front end and back end, they can work on different layers in parallel to speed things up.

The state-actor is the logic that runs inside the state machine. You can consider it to be similar to the stored procedures in web2 databases. That's why we'll have a txn handler as well as SQL related logic here.

All actor code is structured into two main folders: `codec` and `impl`. The `codec` folder contains the definition of types that will be shared and referenced by multiple packages, while the `impl` folder deals with the implementation of the logic.

## Update txn.rs with the new `idea`

Since we haven't changed any code yet, the `codec/src/txn.rs` still contains the task struct that we used in the original tutorial. We need to replace them with the idea struct we designed in the "system_design" step (step 3 in this blog series).

Please remove all existing txns and add the new CreateIdea and VoteIdea txns:

````
#[derive(Debug, Clone, Serialize, Deserialize, AsRefStr, Display)]
pub enum Txns {
    Init {},
    CreateIdea {
        id: String,
        title: String,
        description: String,
        owner: Account,
        auth_b64: String,
        unit: Balance,
    },
    VoteIdea {
        id: String,
        user: Account,
        auth_b64: String,
        price: Balance,
    },
}

````

Replace the `pub struct Task` with :

````
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct Idea {
    pub id: String,
    pub title: String,
    pub description: String,
    pub owner: Account,
    pub create_at: u64,
    pub total_contribution: String,
}
````

Although we don't need the `pub enum Status` , you could conceivably leave it there. It may be needed in some future workflow if a developer wants to implement their own business logic.

### New idea related query and response

In `codec/src/lib.rs` the TaskQueryReqesut and TAskQueryResponse needs to be replaced by :

````
#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
pub struct IdeaQueryRequest {
    pub owner: Option<Account>,
}
#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
pub struct IdeaQueryResponse(pub Vec<txn::Idea>);
````

These are the new requests and responses we designed before.

We've completed some work but still won't be able to compile the state-actor yet. That's because even though we've changed the codec definition, the `impl` struct still hasn't changed from the old Task related struct. Now, please change them on your own. At the same time, you can also remove HttpRequest and anything we no longer need in idea-depot.

I will not explain most of the very straightforward changes, and focus instead on a few of the more important logic.

IdeaQueryRequest handler:

````
impl Handle<IdeaQueryRequest> for Actor {
    async fn handle(&self, req: IdeaQueryRequest) -> Result<IdeaQueryResponse> {
        if req.owner.is_none() {
            Ok(IdeaQueryResponse(sql::query_all_ideas().await?))
        } else {
            Ok(IdeaQueryResponse(
                sql::query_ideas_by_owner(req.owner.unwrap()).await?,
            ))
        }
    }
}
````

This handler handles the IdeaQueryRequest and checks on `req.owner`. If the owner is None it returns all ideas. If this owner is set, only returns the ideas owned by this owner. Using this logic, we can filter the result by the idea's owner. You can see the two functions we'll implement below, `query_all_ideas` and `query_ideas_by_owner`, in sql.rs.

````
pub(crate) async fn query_all_ideas() -> Result<Vec<Idea>> {
    let payload = sql_query_first(
        my_token_id().await?,
        "SELECT * FROM Ideas;".into(),
    )
    .await?;
    let rows = query_select_rows(&payload)?;
    rows.iter().map(|v| parse_idea(v)).collect()
}
````

and 

````
pub(crate) async fn query_ideas_by_owner(owner: Account) -> Result<Vec<Idea>> {
    let payload = sql_query_first(
        my_token_id().await?,
        format!("SELECT * FROM Ideas WHERE owner = '{owner:?}';"),
    )
    .await?;
    let rows = query_select_rows(&payload)?;
    rows.iter().map(|v| parse_idea(v)).collect()
}
````

These are typical SQL queries, not too much to explain.

Besides, we also need a function to query an idea by the idea's id:

````
pub(crate) async fn query_by_id(id: &str) -> Result<Idea> {
    let payload = sql_query_first(
        my_token_id().await?,
        format!("SELECT * FROM Ideas WHERE id = '{id}';"),
    )
    .await?;
    let r = query_first_row(&payload)?;
    parse_idea(r)
}

````

Create task turns to Create Idea:

````
pub(crate) async fn create_idea(
    tsid: Tsid,
    id: String,
    title: String,
    description: String,
    owner: Account,
    unit: Balance,
) -> Result<()> {
    let sql = format!(
        r#"
    INSERT INTO Ideas VALUES (
        '{id}', '{title}', '{description}', '{owner:?}', {create_at}, '{total_contribution}'
    );
        "#,
        create_at = to_short_timestamp(tsid.ts)?,
        total_contribution = unit.to_string(),
    );
    exec_sql(tsid, sql).await
}
````

Since we don't delete ideas, we don't require the `delete_task` function and can delete it. But we will need a new `vote_idea` function.

````
pub(crate) async fn vote_idea(tsid: Tsid, id: String, _user: Account, price: Balance) -> Result<()> {
    let idea = query_by_id(&id).await?;
    let total_contribution = Balance::from_str_radix(&idea.total_contribution, 10)?;
    let new_total_contribution = total_contribution.checked_add(price).ok_or_err("add overflow")?;
    exec_sql(
        tsid,
        format!("UPDATE Ideas SET total_contribution = '{}' WHERE id = '{id}';", new_total_contribution.to_string()),
    )
    .await?;

    Ok(())
}
````

The function parse_task will need to be placed with parse_idea. The idea has different data structure, so we'll need to parse it differently.

````
fn parse_idea(v: &Row) -> Result<Idea> {
    let idea = Idea {
        id: sql_value_to_string(v.get_value_by_index(0).ok_or_err("id")?)?.to_string(),
        title: sql_value_to_string(v.get_value_by_index(1).ok_or_err("title")?)?.to_string(),
        description: sql_value_to_string(v.get_value_by_index(2).ok_or_err("description")?)?
            .to_string(),
        owner: sql_value_to_string(v.get_value_by_index(3).ok_or_err("owner")?)?.parse()?,
        create_at: sql_value_to_u64(v.get_value_by_index(4).ok_or_err("0")?)?,
        total_contribution: sql_value_to_string(v.get_value_by_index(5).ok_or_err("total_contribution")?)?.to_string(),
    };
    Ok(idea)
}

````

The table.sql is used to initilize sql table. It should be as follows:

````
CREATE TABLE Ideas
(
  id                      TEXT UNIQUE,
  title						        TEXT,
  description				      TEXT,
  owner						        TEXT,
	create_at               INTEGER,
  total_contribution      TEXT
);
CREATE INDEX idx_id ON Ideas (id);

````

In txn.rs, we'll update the old task related logic to the idea related logic.  Here’s what CreateIdea and VoteIdea look like:

````
Txns::CreateIdea {
            id,
            title,
            description,
            owner,
            auth_b64,
            unit,
        } => {
            check_account(auth_b64, *owner).await?;
            let (tappstore_ctx, ctx) = account::deposit_for_idea(tsid, base, *owner, *unit, ctx).await?;
            let glue_ctx = new_gluedb_context().await?;
            create_idea(
                tsid,
                id.to_string(),
                title.to_string(),
                description.to_string(),
                *owner,
                *unit,
            )
            .await?;
            CommitContextList {
                ctx_list: vec![
                    CommitContext::new(
                        ctx,
                        glue_ctx,
                        None,
                        None,
                        decode_auth_key(auth_b64)?,
                        txn.to_string(),
                    ),
                    CommitContext::ctx_receipting(tappstore_ctx, txn.to_string()),
                ],
                ..Default::default()
            }
        }
        Txns::VoteIdea { id, user, auth_b64, price } => {
            check_account(auth_b64, *user).await?;
            let (tappstore_ctx, ctx) = account::deposit_for_idea(tsid, base, *user, *price, ctx).await?;

            let glue_ctx = new_gluedb_context().await?;
            vote_idea(tsid, id.to_string(), *user, *price).await?;

            CommitContextList {
                ctx_list: vec![
                    CommitContext::new(
                        ctx,
                        glue_ctx,
                        None,
                        None,
                        decode_auth_key(auth_b64)?,
                        txn.to_string(),
                    ),
                    CommitContext::ctx_receipting(tappstore_ctx, txn.to_string()),
                ],
                ..Default::default()
            }
        }
````

All the code changes above are typical Rust language and aren't specifically related to the TEA Project. I would not make too much explanation. If you have question about gluedb_context or CommitContext, you can find details in the [sdk-doc](https://tearust.github.io/sdk/tea_sdk/).

I believe you have build several times during the changes. No doubt you'll get many errors and as you fix them you should eventually be successfully in building the state-actor.

Right now, there's no way to test the logic in the state actor. We just make sure it compiles successfully. So consider this step only half done. To make our step shorter, I'll still check in the code to the branch called `step7`. Make sure you understand this branch is only half done at this time.
