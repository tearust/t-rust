
Let's focus on the sample-txn-executor folder in this article. This is a brand new folder and it will build the wasm file that will be loaded into the state machine.

## Folder structure

All actors have the same folder structure:
- codec contains all the type definitions
- impl contains all logic

One thing to be mentioned and likely overlooked is the last line in the build.sh.
`cp -r target/wasm32-unknown-unknown/release/sample_txn_executor.wasm ../../dev-runner/local/a-node/`

You may already noticed that the destination folder is **a-node** instead of **b-node** of sample-actor. 

## Codec

In the txn.rs file, we have Task, Status, And Txns definition.

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


Task is the model if you are familar of MVC concept. This object will be mapped to the SQL database for CRUD.

Please note the types "Account, Balance". Those are predefined TEA Project types. You will use them a lot.

Every task will need to have a:
- creator: who create this task. he is also the owner. It is an Account type.
- subject: the title of this task. Such as "Buy me a beer!"
- price: The work who has done this task successfully will receive reward at this price from the owner/creator
- required_deposit: The worker who take a task will need to pay the deposit. If he failed to complete the task, the deposit will be slashed to the price. The final successful worker will take all of them.
- status: See below
- worker: None if no one take this task, or the worker who is current working on or complete this task.

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

Their name explained the meaning.

The most important concept is Txn:
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

Those Txn types are the Transaction object that the hosting node (where b-actor is running) send to the state machine (A-actor) to handle. It is similar to the stored procedure inside the database in Web2 era.

Every txn will have the parameters, and the execution logic of these txn will be inside the `impl` folder. 

In the lib.rs file, we have defined Request and Response as we did in our last step:

A new struct TaskQueryRequests:
```
#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
pub struct TaskQueryRequest {
    pub creator: Option<Account>,
    pub worker: Option<Account>,
    pub status: Option<txn::Status>,
    pub subject: Option<String>,
}
```

and a response that returens a Vec of tasks. The Task has a type `txn::Task`.

They are used in Queries from the hosting nodes. When B-actor wants to query a list of Tasks they can use these conditions. 

Note: In TEA Project future version, a local state cache will be provided to the hosting nodes. So in most cases, there is no need for the hosting node to query list of Tasks from the state machine to reduce the cost.

## Impl

