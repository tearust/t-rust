# System design

## The déjà vu moment!

The ultimate goal of the TEA Project is to establish a framework for existing web2 developers allowing them to build web3 apps with minimal effort. That means if you're already a web2 developer, you don't need to learn blockchain, solidity , smart contracts, consensus, etc. just so you can build web3 apps. The TEA Project helps abstract away many web3 constructs and leaves you to build with tools you're familiar with.

In this step, we'll design the Idea Depot app. The system design of this app is actually identical to your typical web2 app design. Yes, you can still use SQL, and you still need to define data structures, requests, and responses. That's as per usual, which is exactly what we want the TEA Project to feel like using it.



 > 
 > In The Matrix, when inside the simulation, Neo (Keanu Reeves) experiences déjà vu when a black cat passes him twice. [What does this glitch signify?](https://screenrant.com/matrix-neo-black-cat-deja-vu-explained/)

Before venturing into the next step you should familarize yourself with some of the concepts introduced in our [step by step tutorial](https://dev.teaproject.org/020_tutorial). 

## Data structure - Idea

We can compare two very different kinds of system designers:

1. The first types starts from the UI and designs the request/response before then focusing on backend services finally the data structures that support all the operations. 
1. The second type starts from the data structure to make sure it meets all of the business requirements, then the mutation functions, and then the request/response. It's only at the very last stage where they turn their focus to make the UI interactive. 

I happen to be the latter of the two and prefer to start with data structures, so let's start there.

For our business requirements, we know that we'll need to have an `idea` data structure that stores the idea. It basically uses the `task` data structure in the tutorial but with a few additional properties. One of the newly added properties is `total_contribution: String`. This is how many TEA token has been voted towards this idea. Our business requirement doesn't care how many time the same voter votes on the same idea, we only care that they're adding money to their pledge, so we don't need to store the voter's address. This siginificantly reduces the difficulty level. 

So the `Idea` struct looks like:

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

The total_contribution is String type instead of Balance. This is because Balance:u258 is too large to store in SQL. We convert the Balance to String when storing to SQL, then convert it back to u256 after reading it from SQL.

## Txns

There are two transactions to manipulate the idea and its votes. They are 

````
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
````

These are very straightforward. As long as you know a little about the Rust language, you'll understand this. The only thing to note here would be the auth_b64. This is the string that the end user signed when logging in to this Idea_depot app. This string contains what the end user allows this app to do with their assets. We have explained this in the tutorial `login` step. With the limitation of auth string, the app developer cannot do anything beyond what the end user allow them to.

## SQL

SQL is in the TEA Project is as simple as you'd encounter in a traditional web2 app. We'll need SQL statements to create idea in SQL database and increase the vote_num when someone votes for an idea.

The intiial SQLtable use the following SQL statement

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

This SQL is called during SQL intiialization which happens the first time that this TApp was deployed.

Later on when someone creates an idea we have the following logic:

````
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
    
````

If someone votes on an idea to increase their T contribution:

````
 format!("UPDATE Ideas SET total_contribution = '{}' WHERE id = '{id}';", new_total_contribution.to_string()),
````

## Requests and Responses

The following are the Rust code that's needed to generate the SQL statements. The client will need to query the list of ideas and actions to create an idea or vote on an idea.

To create a new idea:

````
#[derive(Debug, Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct CreateIdeaRequest {
  pub uuid: String,
  pub address: String,
  pub tapp_id_b64: String,
  pub auth_b64: String,

  pub id: String,
  pub title: String,
  pub description: String,
  pub unit: String,
}
````

To query the list of ideas:

````
#[derive(Debug, Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct QueryIdeaRequest {
  pub uuid: String,
}
````

The uuid is a system assigned id number for the client to query for a response at a later time. We'll have a detailed explanation on this later in the tutorial.

To vote and contribute to an idea:

````
#[derive(Debug, Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct VoteIdeaRequest {
  pub uuid: String,
  pub address: String,
  pub tapp_id_b64: String,
  pub auth_b64: String,

  pub id: String,
  pub price: String,
}
````

## Handler

There's not too much complex logic going on in this simple app. The QueryIdeaRequest handler for instance just queries the SQL database. The VoteIdeaRequest handler just queries the current total_contribution, then adds their new contribution and then updates SQL. 

You might be wondering how we can use two steps to update the total_contribution? In normal SQL design, we should use a single-step atomic operation "increase" to update total_contribution. In TEA Project, all txn execution is serialized. No matter which hosting node sends the txn to the state machine maintainers, they will be sorted based on True Timestamp in every state machine. Because the True Timestamp comes from Global sync atomic clock (GPS), all maintainers will have the same sequence of all txns. The state machine will execute those sequencial txns one by one. There is no parallel execution. You don't need to worry about multiple SQL statements executed in parallell causing race conditions. This model is more like the Ethereum EVM rather than SQL database. 

## Workflow

There's nothing special to explain about the workflow. This is the typical TApp workflow which is the same as the tutorial app. Basically:

* Front end browser sends request to hosting node.
* Hosting node handles the request. If needed, it will generate txn to the state machine maintainer.
* State machine maintainers sort the txn from all hosting nodes. After a short buffer delay, all txns are executed one after another.
* State machine sends back the result of txn execution.
* The front end browser keeps querying for the result until the hosting node receives and relays the result back to the front end.
* Front end shows the UI update to the user.

In the next step, we'll start to clone the code from the tutorial to make sure it can compile and run locally. Then we'll modify the code to fit all the business logic specific to our TApp.
