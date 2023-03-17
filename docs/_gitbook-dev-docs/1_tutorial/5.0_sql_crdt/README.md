In this tutorial, we will learn how to write SQL scripts in TApp development.

The goal of this step would be:

* User can get free test token using Faucet
* User can create a task then stored in database
* User can edit a task then save changes
* User can delete a task
* User can list all tasks 

Using SQL is a major difference from most other web3 platform. TEA Project is not a blockchain, it has distributed SQL instances in every state machine node. The `Proof-of-time` consensus can make sure all nodes reach the same state (strong consensus) after a time buffer. 

For the developers, it is as simple as what they did in traditional web2 development as if there is a SQL database under the hood. The only difference would be you have to understand the changes will be taking effect after a time buffer. This time buffer is set to  3 seconds during TEA Project beta. After this time buffer, the state change will be shown in your next query. 

Although get free test token should be put into the next tutorial step (the state machine transfer), we leave the code in the `sql` branch simple the user need some token to pay gas. 

## Understand the sample-txn-executor actor

You will need to `git checkout sql` to the SQL branch. You will notice that a new folder `sample-txn-executor` has been added. This actor is usally called "A-actor" internally. The previous `sample-actor` is usually called "B-actor". Although the official names for A-actor should be state machine actor and B-actor should be called hosting actor. Some old documents still use the old name, just to be understood.

Similar to what the 3-tiers architect in the traditional web2 development, the hosting actor is the lambda function running inside the Web Server, but the state machine actor is running inside the state machine as if a database stored procedure.

## Client \<-> Hosting actor \<-> State machine actor workflow

The common request -> response work flow would be:

* Browser(client) send request to hosting node
* Hosting actor inside hosting node handles the reqeust.
* If the hosting actor sample-actor can response immediately, it will response directly back to the client. The request -> response loop ends.
* If the hosting actor cannot resposne immedaitely, it usually sends another txn to the state machine node. As the same time response a UUID back to the browser(client). The client will know it has to query the result at a later time using the UUID.
* The state machine node received the txn from hosting node. The txn is queued in the Proof of Time coveryor, sorted and eventually in-turn executed by the sample-txn-executor actor.
* The sample-txn-executor may change the state in the state machine in case of "command", or not change at all in the case of "query". The state machine will update the hosting node a result that any error or successfully.
* The client will periodically query the hosting node for the result of the previous request using the UUID.
* If the hosting has got the update from the state machine node, and update the result associated with the UUID, it will send back the resposne back to the client.
* The browser(client) will get a notification that the txn has been executed, with result either successful or error.

In this step, we will use SQL as an example to demo how this work flow works. All other type of reqeust will work the same way.

## Build and test

It is the same way of building but you will need to build two wasm files that located in `sample-actor` and `sample-txn-executor` folder. Also need to copy the target wasm file to the dev-runner/local/a-node folder. Note: unlike sample_actor.wasm goes to the b-node, this sample_txn_executor.wasm goes to the **a-node**. This is because it runs in state machine. 

Start the dev-runner by running `docker compose up`.

Start the front end by running `npm i` and `npm start` from the sample-front-end folder.

Go to http://localhost:3200, you should see:

![Pasted image 20230315094127.png](../../../Pasted%20image%2020230315094127.png)
[Pasted image 20230315093816.png](../../../Pasted%20image%2020230315093816.png)

You may see the new added "Task" page. Most of our features would be in that page. But before we try the latest features, let's test the existing features from previous "master " and "login" steps, make sure they are still running without any breaking changes.

Say-hello still works:
![Pasted image 20230315093943.png](../../../Pasted%20image%2020230315093943.png)

Now login as you did in previous step. Use "faucet" to add 1000T to your account. This test token will be enough for testing purpose. Now your account should look like this:
![Pasted image 20230315094045.png](../../../Pasted%20image%2020230315094045.png)

Great, you previous steps are still running as expected. Now move to the new features by click "Task".

The UI is very simple, just add new tasks. You should see them in the list.

![Pasted image 20230315094303.png](../../../Pasted%20image%2020230315094303.png)

You can try to remove it as well. It should work as expected.

You can logout and login again to verify your previous stored task is still there. So your work has been stored into the SQL database in our state machine. 

Great!, now let's move on to the code walkthrough and figure out how it works.
