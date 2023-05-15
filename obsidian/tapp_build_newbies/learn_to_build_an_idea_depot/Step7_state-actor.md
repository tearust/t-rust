In our last step, we have create a brand new project called idea-depot from the original tutorial-v1 reward branch. Starting from this step, we will start to add new business logic to turn the tutorial-v1 to the idea-depot.

There are two types of developers. Type one build the front end first, so that they can see the UI change and they can use the UI to test the backend or state machines layer. Type two are from bottom up. They starts from the the lowest layer - the state machine layer, then the backend web services, the last the front end. They will need to make sure the under layer works before add upper layer to call this layer. Since I used the type-two sequence when design the data structure, I would do ths same way. When there are several developers in a team that work on front end and back end, they can work on different layers parralelly to speed up.

The state-actor is the logic runs inside state machines. You can consider it is the stored procedure in web2 database. That is why we will have txn handler and SQL related logic here.

All actor code structure has two main folders: `codec` and `impl`. Codec is the definition of types that will be shared and references by multiple packages. Impl is the implementation of the logic.

### Update txn.rs with new `idea`

Since we have not changed any code yet, the `codec/src/txn.rs` are still the tasks struct that we used in the tutorial. We need to replace them with the idea struct we designed in the "system_design" step.

Please remove all existing Txns and add new CreateIdea and VoteIdea txn:
```
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

```

Replace the `pub struct Task` with :
```
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct Idea {
    pub id: String,
    pub title: String,
    pub description: String,
    pub owner: Account,
    pub create_at: u64,
    pub total_contribution: String,
}
```

Although we do not need the `pub enum Status` , but it doesn't hurt to leave them there. We may need them in our future business logic. Assuming we will add a workflow for some developer to implement this idea.

### New idea related query and response

In `codec/src/lib.rs` the TaskQueryReqesut and TAskQueryResponse need to be replaced by :
```

#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
pub struct IdeaQueryRequest {
    pub owner: Option<Account>,
}
#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
pub struct IdeaQueryResponse(pub Vec<txn::Idea>);

```

These are the new request and response we designed before.

So far, you cannot compile the state-actor yet, because we have changed the codec definition, but the `impl` is still not changed, it still try to find the old Task related struct. Now, please change them on your own. At the same time, you can also remove HttpRequest and anything we will no longer need in idea-depot.

I will ignore most very straightforward changes, and focus on a few most important logic.

IdeaQueryRequest handler:
```
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
```

This handler handle the IdeaQueryRequest, check if `req.owner`. If the owner is None if returns all ideas. If this owner is set, only returns the ideas owned by this owner. Using this logic, we can filter the result by the idea's owner. You can see two functions we will implement below `query_all_ideas` and `query_ideas_by_owner` in sql.rs

``` 
pub(crate) async fn query_all_ideas() -> Result<Vec<Idea>> {
    let payload = sql_query_first(
        my_token_id().await?,
        "SELECT * FROM Ideas;".into(),
    )
    .await?;
    let rows = query_select_rows(&payload)?;
    rows.iter().map(|v| parse_idea(v)).collect()
}
```
and 
```
pub(crate) async fn query_ideas_by_owner(owner: Account) -> Result<Vec<Idea>> {
    let payload = sql_query_first(
        my_token_id().await?,
        format!("SELECT * FROM Ideas WHERE owner = '{owner:?}';"),
    )
    .await?;
    let rows = query_select_rows(&payload)?;
    rows.iter().map(|v| parse_idea(v)).collect()
}
```

These are typical SQL queries, not too much to explain.

Besides, we also need a function to query an idea by idea's id
```
pub(crate) async fn query_by_id(id: &str) -> Result<Idea> {
    let payload = sql_query_first(
        my_token_id().await?,
        format!("SELECT * FROM Ideas WHERE id = '{id}';"),
    )
    .await?;
    let r = query_first_row(&payload)?;
    parse_idea(r)
}

```

Create task turns to Create Idea
```
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
```

Since we do not delete idea in our bueinsss logic requirement, so delete the delete_task. But we need a new vote_idea function

```
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
```

The function parse_task will need to be placed with parse_idea. The idea has different data structure, so we will need to parse.

```
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

```

The table.sql is used to initilize sql table. It should be 
```
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

```

In txn.rs replace old task related logic to idea: `CreateIdea` and `VoteIdea`

```
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
```

All the code changes above are typical Rust language, not too much related to the TEA Project. I would not make too much explanation. If you have question about gluedb_context or CommitContext. You can find details in the sdk-doc.

I believe you have build several times during the changes. No doubt you will get many errors and have you fixed all of them. Now you should be successfully build the state-actor now.

Right now, there is no way to test the logic in state actor. We just make it compiled successfully. So this is only half done. To make our step shorter, I will still check in the code to the branch called `step7`. Make sure you understand this branch is just a half done.
