# System design

## The déjà vu moment!
The ultimate goal of the TEA Project is a framework for existing web2 developer to build web3 apps with minimal efford. That means if you are a web2 developer already, you do not need to learn blockchain, solidity , smart contract, consensus, etc, you can still make such web3 apps based on what you are familiar with already.

In this step, we will design the Idea Depot app. I bet you have notice that the system design is identical to your typical web2 app design. Yes, you can still use SQL, you still need to define data structure, requests and response. Nothing new. This is exactly what TEA Project is for.


![[Pasted image 20230511165644.png]]

> In The Matrix, when inside the simulation, Neo (Keanu Reeves) experiences déjà vu when a black cat passes him twice. [What does this glitch signify?](https://screenrant.com/matrix-neo-black-cat-deja-vu-explained/)


Even you have not read our TEA Project developer tutorial, you won't have difficulty understand this step. But you will have to learn the tutorial before our next step. 

## Data structure - Idea

There are two type of system designer. One goes from the UI UX, design the request/response, then backend services, the last the data structures that support all the operations. Another one goes from the data structure to meet all busienss requirements, then the mutation functions, then the request/response, the last the UI that make human interactive. I happen to be the latter. So let's start with the data structure.

From our business requirement, we know we will need to have an `idea` data structure that store the idea. It was the `task` in the tutorial but a few additional properties. One of the new added property is `total_contribution: String`. This is how many TEA token has been voted to this idea. Our business requirement doesn't care how many time the same voter votes to the same idea as long as he keep adding money to it, so we do not need to store the voter's address. This siginificantly reduce the difficulty level. 

So the `Idea` struct looks like:
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
The total_contribution is String type instead of Balance. This is because Balance:u258 is too large to store in SQL. We convert the Balance to String when store to SQL, then convert it back to u256 after read from SQL.

## Txns

There are two transactions to munipulate the idea and its votes. They are 

```
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
```


They are very straightforward. As long as you know a little about Rust language, you will understand this. The only thing to note here would be the auth_b64. This is the string that the end user signed when login to this Idea_depot app. This string contents what the end user allow this app to do with their assets. We have explained this in the tutorial `login` step. With the limitation of auth string, the app developer cannot do anything beyond the end user allow them to.

## SQL

As simple as it was in a traditional web2 app. We will need SQL statements to create idea in SQL database and increase the vote_num when someone vote for an idea.

The intiial SQLtable use the following SQL statement
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
This SQL is called during SQL intiialization which happened the first time this TApp was deployed.

Later one, when someone create an idea


```
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
    
```
    
If someone vote an idea to increase the contribution

```
 format!("UPDATE Ideas SET total_contribution = '{}' WHERE id = '{id}';", new_total_contribution.to_string()),
 ```

These are the Rust code to generate the SQL statements.

## Requests and Responses

The client will need to query the list of ideas and action to create idea or vote on an idea.

To create an new idea
```
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
```

To query the list of ideas
```
#[derive(Debug, Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct QueryIdeaRequest {
  pub uuid: String,
}
```
The uuid is a system assigned id number for the client to query response at a later time. We have detail explanation on this in the tutorial.

To vote and contribute to an idea
```
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
```

## Handler

There are not too much complex logic for this simple app. For QueryIdeaRequest handler just query the SQL. For VoteIdeaRequest handler just query the current total_contribution, then add the new contribution then update SQL. 

You may want to ask why can we use two steps to update the total_contribution? In normal SQL design, we should use a singel-step atomic operation "increase" to update total_contribution. In TEA Project, all txn execution is serialized. No matter which hosting node send the txn to the state machine maintainers, they will be sorted based on True Timestamp in every state machine. Because the True Timestamp comes from Global sync atomic clock (GPS), all maintainer will have the same sequence of all txns. The state machine will execute those sequencial txns one by one. There is no parallel execution. You do not need to worry about multiple SQL statements executed parallelly cause race condition. This model is more like Ethereum EVM rather than SQL database. 

## Workflow

There is nothing to explain in the workflow. This is the typical TApp workflow which is the same as the tutorial app. Basically:
- Front end browser send request to hosting node
- Hosting node handle the request. If needed, it will generate txn to the state machine maintainer
- State machine maintainers sort the txn from all hosting nodes, after a short buffer delay, all txns are executed one after another.
- State machine send back the result of txn execution
- Front end browser keep query the result until hosting node receive and relay the result back to the front end.
- Front end shows the UI update to user

Next step, we will start to clone the code from tutorial. Make sure it can compile and run locally. Then start to modify the code to fit all business logic specific to our TApp.