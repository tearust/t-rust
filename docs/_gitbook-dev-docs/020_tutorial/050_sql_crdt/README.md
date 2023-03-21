# SQL

In this tutorial, we'll learn how to write SQL scripts in TApp development.

The business goal of this step would be:

* User can get free test token using the faucet.
* User can create a task which is stored in the database.
* User can edit a task then save changes.
* User can delete a task.
* User can list all tasks. 

Being able to use SQL is a major advantage that TEA Project has over other web3 platforms. TEA Project is not a blockchain; it has distributed SQL instances in every state machine node. The **Proof-of-time** consensus can make sure all [nodes](../../z_glossary/hosting_cml.md) reach the same [state](../../z_glossary/state.md) (strong [consensus](../../z_glossary/consensus.md)) after a time buffer elapses. 

For developers, it's as simple as what they did in traditional web2 development as if there's an SQL database under the hood. The only difference would be that the changes will be taking effect after a time buffer. This time buffer is set to 3 seconds during the TEA Project beta. After this time buffer, the state change will be shown in your next query. 

Although the step to get free test tokens should technically be in the next tutorial step (the state machine transfer), we'll leave the code in the `sql` branch simply because the user needs some tokens to pay gas. 

## Understand the sample-txn-executor actor

You'll need to `git checkout sql` to the SQL branch. You'll notice that a new folder `sample-txn-executor` has been added. This [actor](../../z_glossary/actor.md) is usally called "A-actor" internally. The previous `sample-actor` is usually called "B-actor". Although the official names for A-actor should be state machine actor and B-actor should be called hosting actor. Just to be clear, some old documents will still use the old "A" and "B" node names.

Similar to 3-tier architecture in traditional web2 development, the hosting actor is the lambda function running inside the Web Server, while the state machine actor is running inside the [state machine](../../z_glossary/state_machine.md)  similar to a database stored procedure.

## Client \<-> Hosting actor \<-> State machine actor workflow

The common request -> response work flow would be as follows:

* Browser (client) sends requests to a hosting node.
* Hosting actor inside the hosting node handles the request.
* If the hosting actor sample-actor can't respond immediately, it will respond directly back to the client. The request -> response loop ends.
* If the hosting actor cannot respond immediately, it usually sends another txn to the state machine node. At the same time a UUID response is sent back to the browser (client). The client will know it has to query the result at a later time using the UUID.
* The state machine node receives the txn from the hosting node. The txn is queued in the Proof of Time conveyor, sorted and eventually executed in turn by the sample-txn-executor actor.
* The sample-txn-executor may change the state in the state machine in case of a [command](../../z_glossary/commands.md), or not change the state at all in the case of a [query](../../z_glossary/query.md). The state machine will update the hosting node with the result, either success or an error.
* The client will periodically query the hosting node for the result of the previous request using the UUID.
* The hosting node will eventually get the update from the state machine node along with the result associated with the UUID. After the hosting node has the result, the next client query will receive the response back of the UUID and the result.
* The browser (client) will get a notification that the txn has been executed along with the execution status, either successful or an error.

In this step, we'll use SQL as an example to demo how this workflow works. All other types of reqeusts will work the same way.

## Build

In your local `tutorial-v1` repo, you'll need to build two wasm files that are located in the `sample-actor` and `sample-txn-executor` folders. The target wasm files will be copied to the `dev-runner` repo: specifically to `/local/b-node` and to `dev-runner/local/a-node` respectively. Note: unlike the `sample_actor.wasm` file that goes to the **b-node**, this `sample_txn_executor.wasm` goes to the **a-node**. This is because it runs in the state machine. 

Start the dev-runner by running `docker compose up`.

Start the front end by running `npm i` and `npm start` from the sample-front-end folder.

## Admin page and initialization

Visiting http://localhost:3200, you should see:

![Pasted image 20230317093125.png](../../../Pasted%20image%2020230317093125.png)

[Pasted image 20230315093816.png](../../../Pasted%20image%2020230315093816.png)

You may see the newly added "Task"and "Admin" page. Most of our features would be in the Task page, but what's the "Admin" page? 

Actually there shouldn't be this type of Admin page in a real TApp We have this Admin page simply because we're running a local development environment called Dev-Runner. We want to use the two buttons "Init AppToken" and "Init TApp db" to simulate the Deploy and Init TApp steps in the real Developer Portal. The Dev-Runner is a simplified version of the TEA-Runtime. There's no Developer Portal built-in, but we still need to initialize the TApp token and TApp database before a TApp can be used. 

So please always click those two buttons in the Admin page whenever:

* It's the first time you're running this TApp in Dev-Runner. Or,
* Everytime after you've deleted the `.tokenstate` local state persistent storage.

You'll need to delete the `.tokenstate` everytime you modify your code that's related to any state or SQL database. Consider it a "purge" operation. After such a purge, your whole state and SQL databse would be **brand new**. That's why you'll need to initialize it before using your TApp. In the real production environment, there won't be such a "refresh". It'll only be initialized once at your TApp initial deploy.

You'll only need to run those two initialization tasks once. If you click it the second time, you'll be prompted with an error but it won't cause any damage at all. So if you're not sure if you've initialized it or not, feel free to click them again, and take any generated errors as a harmless sign you've already completed these steps.

## Run the "sql" branch

But before we try the latest features, let's test the existing features from the previous "master " and "login" steps, making sure they're still running without any breaking changes.

"Hello world" still works:

![Pasted image 20230315093943.png](../../../Pasted%20image%2020230315093943.png)

Now login as you did in the previous step. Use the "faucet" to add 1000T to your account which should be enough for testing purposes. Now your account should look like this:

![Pasted image 20230315094045.png](../../../Pasted%20image%2020230315094045.png)

Great, your previous steps are still running as expected. Now move to the new features by clicking "Task".

The UI is very simple, just add new tasks. You should see them in the list.

![Pasted image 20230315094303.png](../../../Pasted%20image%2020230315094303.png)

You can try to remove tasks as well, and it should work as expected.

You can logout and login again to verify your previous stored tasks are still there. So your work has been stored into the SQL database in our state machine. 

Great! Now let's move on to the code walkthrough and figure out how it works.
