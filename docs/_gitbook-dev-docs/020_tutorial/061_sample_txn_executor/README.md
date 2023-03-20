The newly added file and major changes in this step are in the `account.rs` file.

In account.rs, we handled the following txns:

* deposit_for_task: Pay deposit when taking a task.
* rollback_deposit:  Move the price back to the owner in case of "reject" operation.
* reward_owner: The successful worker takes the reward. 

All the function code is straightforward and follows the same pattern:

* Input a `mut ctx`. 
* Get the amount as well as payer and payee addresses.
* Call `move` or `cross_move` function to transfer funds.
* The ctx is mutable, so it contains all the change on the state. The caller will handle the ctx.

Let's move to the caller, the txn.rs. We use the TakeTask as an example, but all others txns follow the same pattern:

````
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
````

First, get the task. If the task status is not "new" then throw an error. That's because only new tasks can be taken.

If the task has a worker, throw an error because the task has someone working on it.

`check_account(auth_b64, *worker).await?;` guard the current login user as the input worker parameter.

`let glue_ctx = new_gluedb_context().await?;` starts an SQL transaction. We should have such a preface line everytime we run any SQL transactions whenever you process a SQL txn.

````
let (tappstore_ctx, ctx) =
                account::deposit_for_task(tsid, base, *worker, task.required_deposit, ctx).await?;
````

This function `deposit_for_task` has a `ctx` input, also outputs  `tappstore_ctx` and `ctx`. This is very important. Ctx is the object that records all the changes the code logic "should" apply to the state. But until commited, the changes are only stored in the Ctx without actually modifying the state. If anything is wrong during the ctx execution, the code can simply throw an error and return, no changes will be applied to the state and the state will remain unchanged. 

In order to keep all changes in `ctx` and commit at once to modify `ctx` at a later time, ALL functions will have ctx as an input as well as returning `ctx` to the caller. 

In this function, the output has another tappstore_ctx rather than the sole input ctx. That's because the tappstore's state will be changed during the execution. We'll need to commit both ctx for this TApp AND the context of TAppStore. Why will we change the state of the TAppStore? It's because the deposit and TEA account balance are stored in the TAppStore state. This is designed for easier TApp development. In most cases, the TApp doesn't need to maintain a TEA token state in their own state. Nor does the user need to topup TEA tokens to all the TApps they're using. 

`take_task` is a function to execute sql scripts. The code is in sql.rs:

````
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
````

It's to change the table to mark that a worker has taken this task.

Lastly, at the end of this [txn](../../z_glossary/txn.md), return the CommitContextList:

````
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
````

All matching branches in the txn executor will need to return such CommitContextList. This list includes all the changes during the execution. Eventually, these changes will be applied using a Commit function so that the state and SQL database can be permanently changed. 

For the details of CommitContextList, please refer to the Developer Documents.

## Other minor changes

We can skip the table.sql because these are all standard SQL scripts, for indexing, table creating etc. SQL is not in the scope of our tutorial.

In error.rs, you can find a few newly added errors. They're very straightfoward.
