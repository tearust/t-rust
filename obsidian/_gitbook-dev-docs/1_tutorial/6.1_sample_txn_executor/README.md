
The new added file and major changes are in this step is the `account.rs` file.

In this account.rs, we handled the following txns:
- deposit_for_task: Pay deposit when take a task
- rollback_deposit: The move the price back to the owner in case of remove operation
- reward_owner: The winner take the reward. 

All the function code are very straightforward. The all follow the same pattern:
- Input a `mut ctx`. 
- Get the amount and payer payee address
- Call mov or cross_move function to transfer fund
- the ctx is mutable, so it contains all the change on the state. The caller will handle the ctx

Let's move to the caller, the txn.rs. We use the TakeTask as one example, all others txns are following the same pattern:

```
Txns::TakeTask {
            subject,
            worker,
            auth_b64,
        } => {
            let task = task_by_subject(subject).await?;
            if task.status != Status::New {
                return Err(TxnErrors::TakeTaskFailed.into());
            }
            if let Some(worker) = task.worker {
                return Err(TxnErrors::TaskInprogress(task.subject, worker).into());
            }
            check_account(auth_b64, *worker).await?;
            let glue_ctx = new_gluedb_context().await?;

            let (tappstore_ctx, ctx) =
                account::deposit_for_task(tsid, base, *worker, task.required_deposit, ctx).await?;
            take_task(tsid, subject, *worker, task.required_deposit).await?;
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

First, get the task. If the task status is not "new" throw an error. That is because only new task can be taken.

If the task has a worker, throw an error, because the task has some one working on it.

`check_account(auth_b64, *worker).await?;` guard the current login user is the input worker parameter.

`let glue_ctx = new_gluedb_context().await?;` starts a SQL transaction. We should have such line everytime when we will run any SQL transaction while processing txn.

```
let (tappstore_ctx, ctx) =
                account::deposit_for_task(tsid, base, *worker, task.required_deposit, ctx).await?;
```
            
This function `deposit_for_task` has a `ctx` input, also output  `tappstore_ctx` and `ctx`. This is very important. Ctx is the object that record all the changes the code logic "should" apply to the state. But until commit, the changes only stored in the Ctx without actually modify the state. If anything wrong during the ctx execution, the code can simple throw an error and return, no changes will be apply to the state. The state remains no change. 

In order to keep all changes in the Ctx and commit at once at a later time, ALL functions will has ctx input and return to the caller. 

In this function, the output has another tappstore_ctx rather than the sole input ctx, that is because the tappstore's state will be changed during the execution. We will need to commit both ctx for this TApp AND the context of TAppStore. Why will change the state of TAppStore? that is because the deposit and TEA account balance are stored in the TAppStore state. This is designed for easier TApp development. In most cases, the TApp doens't need to maintain a TEA token state in their own state. Nor the user need to topup TEA token to all TApps they are using. 

`take_task` is a function to execute a sql scripts. The code is in the sql.rs:
```
pub(crate) async fn take_task(
    tsid: Tsid,
    subject: &str,
    worker: Account,
    required_deposit: Balance,
) -> Result<()> {
    exec_sql(
        tsid,
        format!(
            r#"
            UPDATE Tasks SET 
                status = '{}',worker = '{worker:?}' 
                WHERE subject = '{subject}';
            INSERT INTO TaskExecution VALUES (
                '{subject}', '{worker:?}', '{required_deposit}'
            );
               "#,
            Status::InProgress
        ),
    )
    .await
}
```
It is nothing new, but to change the table to make the works has taken this task.

Last, at the end of this txn, return the CommitContextList:
```
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
```


All match branches in the txn executor will need to return such CommitContextList. This list include all the changes during the execution. Eventually, these changes will be apply using a Commit function so that the state and SQL database can be permenently changed. 

For the detail of CommitContextList, please refer to the Developer Document.

## Other minor changes

We can skip the table.sql because these are all standard SQL scripts, for indexing, table creating etc. SQL is not in the scope of our tutorial.

In error.rs, you can find a few new added Errors. They are very straightfoward.




