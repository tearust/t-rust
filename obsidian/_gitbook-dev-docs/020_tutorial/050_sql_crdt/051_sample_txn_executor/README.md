# Sample Txn Executor
Let's focus on the sample-txn-executor folder in this article. This is a brand new folder and it'll build the wasm file that will be loaded into the [state machine](../../../z_glossary/state_machine.md) .

## Folder structure

All actors have the same folder structure:

- codec contains all the type definitions.
- impl contains all the logic.

One thing to be mentioned and likely overlooked is the last line in the build.sh file:
`cp -r target/wasm32-unknown-unknown/release/sample_txn_executor.wasm ../../dev-runner/local/a-node/`

You may have already noticed that the destination folder is an **a-node** instead of a **b-node** of the sample-actor. 

## Codec

In the txn.rs file, we have Task, Status, And Txns definitions.

```#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct Task {
    pub creator: Account,
    pub subject: String,
    pub price: Balance,
    pub required_deposit: Balance,
    pub status: Status,
    pub worker: Option<Account>,
}
```


Task is the model if you're familar with the MVC concept. This object will be mapped to the SQL database for CRUD.

Please note the types "Account, Balance". Those are predefined TEA Project types. You'll use them a lot.

Every task will need to have a:

- creator: who create this task, who is also the owner. It's an Account type.
- subject: the title of this task. Such as "Buy me a beer!"
- price: The worker who has done this task successfully will receive the reward price from the owner/creator.
- required_deposit: The worker who takes a task will need to pay the deposit. If they fail to complete the task, the deposit will be slashed and rolled into the price. The final successful worker will take the augmented price.
- status: See below.
- worker: None if no one takes a particular task, or the worker who is currently working on or completes a particular task.

The status is:

```
#[derive(
    Debug, Clone, Copy, Serialize, Deserialize, PartialEq, Eq, AsRefStr, EnumString, Display,
)]
pub enum Status {
    New,
    InProgress,
    WaitForVerification,
    Done,
}
```

Their name explains the meaning.

The most important concept is [txn](../../../z_glossary/txn.md):

```
#[derive(Debug, Clone, Serialize, Deserialize, AsRefStr, Display)]
pub enum Txns {
    Init {},
    CreateTask {
        task: Task,
        auth_b64: String,
    },
    DeleteTask {
        subject: String,
        auth_b64: String,
    },
    VerifyTask {
        subject: String,
        failed: bool,
        auth_b64: String,
    },
    TakeTask {
        subject: String,
        worker: Account,
        auth_b64: String,
    },
    CompleteTask {
        subject: String,
        auth_b64: String,
    },
}

```

Those Txn types are the Transaction objects that the hosting nodes (where the b-actor is running) send to the state machine (A-actor) to handle. It's similar to the stored procedures inside of databases of the web2 era.

Every txn will have the parameters, and the execution logic of these txn will be inside the `impl` folder. 

In the lib.rs file, we have defined Requests and Responses as we did in our last step:

There's a new struct TaskQueryRequests:

```
#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
pub struct TaskQueryRequest {
    pub creator: Option<Account>,
    pub worker: Option<Account>,
    pub status: Option<txn::Status>,
    pub subject: Option<String>,
}
```

and a response that returns a Vec of tasks. The Task has a type `txn::Task`.

They're used in Queries from the hosting nodes. When B-actor wants to query a list of Tasks they can use these conditions. 

Note: In TEA Project future versions, a local state cache will be provided to the hosting nodes. So in most cases, there's no need for the hosting node to query the list of Tasks from the state machine, which will ultimately reduces the cost.

## Impl

### manifest.yaml added new access

Compared with the previous steps, you'll notice there are lots of new access items added:

```
access:
  - tea:adapter
  - tea:statereceiver
  - tea:tokenstate
  - tea:replica
  - tea:crypto
  - tea:nitro
  - tea:env
  - tea:keyvalue
  - com.tea.keyvalue-actor.manager
  - com.tea.tappstore-actor
  - com.tea.ra-actor

```

You can go to the developer documentation for the usage for each provider actor. Because the `sql` branch has added many system features such as fund transfer, sql access, storage etc, it's required to claim the list in the manifest. Otherwise, the access will be banned by the tea-runtime.

### tables.sql is a standard sql script

This is the first time you'll find a non rs file in the src folder. This is a standard SQL file. It's nothing but a script to create table. 

```
CREATE TABLE Tasks
(
  subject						TEXT UNIQUE,
  creator						TEXT,
  worker						TEXT NULL,
  status						TEXT,
	price 						TEXT,
	required_deposit	TEXT
);
```

To simplify our tutorial, we didn't use any 3rd party MVC tools to build sql scripts for us. We use pure SQL which is easily understood by developers. When you're building your own TApp, feel free to use whatever existing web2 tools you're familiar with. They should all work well with the TEA Project.

You may be wondering where are other SQL scripts? We should use Create, Read, Update, Delete scripts. Well, they're inside the rs code as string and you'll see them soon.


### Add new errors into error.rs

Because we're now handling the txn, there will be errors during txn execution. We need to define them inside of `error.rs`:

```
#[derive(Debug, Error)]
pub enum TxnErrors {
    #[error("Account {0:?} is not allowed to operate task")]
    InvalidAccount(Account),

    #[error("Task {0} already token by {1:?}")]
    TaskInprogress(String, Account),

    #[error("Task can only be deleted when status is new or done")]
    DeleteTaskFailed,

    #[error("Task can only be verified when status is wait for verification")]
    VerifyTaskFailed,

    #[error("Task can only be taken when status is new")]
    TakeTaskFailed,

    #[error("Task can only be finished when status is in process")]
    CompleteTaskFailed,
}


```

Note, don't forget to add to the define_scope macro too:

```
define_scope! {
    Impl: SampleActor {
        HttpActionNotSupported => @SampleActor::HttpActionNotSupported;
        TxnErrors => @SampleActor::TxnErrors;
    }
}
```

### Execute transactions in txn.rs

Most of the logic is here inside the txn.rs function `txn_exec`.

You can see all txns defined in the codec have been handled in a `match` branch, then return a commit_ctx. The commit_ctx is a Context type which records all changes during the txn execution. However, before the commit_ctx is finally commited at the last of the `txn_exec` function, no actual changes happened in the state. That means, at any time, if the execution failed for whatever reason, the state will **NOT** be changed. 

Let's use CreateTask as an example:

```
        Txns::CreateTask { task, auth_b64 } => {
            // check_account(auth_b64, task.creator).await?;
            let glue_ctx = new_gluedb_context().await?;
            create_task(tsid, task).await?;
            CommitContext::new(
                ctx,
                glue_ctx,
                None,
                None,
                decode_auth_key(auth_b64)?,
                txn.to_string(),
            )
        }
```

This txn only run a SQL scripts, it's very simple. First `new_gluedb_context()` will generate a new glue_ctx. It also starts an SQL Transaction. If anything failed before the commit, no change will be written to the SQL database. 

The SQL scripts is inside the `create_task` function. 

```
pub(crate) async fn create_task(tsid: Tsid, task: &Task) -> Result<()> {
    exec_sql(
        tsid,
        format!(
            "INSERT INTO Tasks VALUES ('{}','{:?}',NULL,'{}','{}','{}');",
            task.subject,
            task.creator,
            Status::New,
            task.price,
            task.required_deposit
        ),
    )
    .await
}
```

The function `exec_sql` will run the SQL scripts.

In TEA Project, the SQL engine is [GlueSQL](https://github.com/gluesql/gluesql). This is not a fully featured SQL engine, so please review our documentation entry for [GlueSQL](../../../z_glossary/gluesql.md) ) for more details. In our tutorial, we only use very basic SQL features. For example, we didn't use auto increase ID but instead used the subject as ID. This is not ideal but good enough to demonstrate the logic. Teaching SQL is not the purpose of this tutorial.

Please make sure `sql_init` is called at `Txns::Init`. 

```
       Txns::Init {} => {
            sql_init(tsid).await?;
            CommitContext::new(
                ctx,
                None,
                None,
                None,
                GOD_MODE_AUTH_KEY,
                txn.to_string(),
            )
        }
```

TODO:// GOD_MODE_AUTH_KEY will be replaced later

## lib.rs

Lib.rs is the entry point of the whole sample-txn-executor. It has the same structure as the sample-actor. 

First, we should also list all [txns](../../../z_glossary/txn.md) that we can handle:

```
impl Handles<()> for Actor {
    type List = Handle![
        Activate,
        PreInvoke,
        HttpRequest,
        TaskQueryRequest,
        ExecTxnCast,
        ActorTxnCheckMessage
    ];
}
```


**HttpRequest** is a special request that we created for the local dev-runner only. In the real production this will not exist. The purpose of adding http request to the sample-txn-executor is for easy CURL / Postman testing. In the real production environment, all txns are sent from the hosting nodes (B-actor). You have to have a B node to test the Txns, which causes additional complexities. Using this "mock" http request, you can write your own local test code. Especially when dealling with SQL, it's hard to write SQL in unit tests.