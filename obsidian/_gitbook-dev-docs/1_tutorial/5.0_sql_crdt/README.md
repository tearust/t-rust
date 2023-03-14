
In this tutorial, we will learn how to write SQL scripts in TApp development.

The goal of this step would be:
- User can create a task then stored in database
- User can edit a task then save changes
- User can delete a task
- User can list all tasks 

Using SQL is a major difference from most other web3 platform. TEA Project is not a blockchain, it has distributed SQL instances in every state machine node. The `Proof-of-time` consensus can make sure all nodes reach the same state (strong consensus) after a time buffer. 

For the developers, it is as simple as what they did in traditional web2 development as if there is a SQL database under the hood. The only difference would be you have to understand the changes will be taking effect after a time buffer. This time buffer is set to  3 seconds during TEA Project beta. After this time buffer, the state change will be shown in your next query. 

## Understand the sample-txn-executor actor

You will need to `git checkout sql` to the SQL branch. You will notice that a new folder `sample-txn-executor` has been added. This actor is usally called "A-actor" internally. The previous `sample-actor` is usually called "B-actor". Although the official names for A-actor should be state machine actor and B-actor should be called hosting actor. Some old documents still use the old name, just to be understood.

Similar to what the 3-tiers architect in the traditional web2 development, the hosting actor is the lambda function running inside the Web Server, but the state machine actor is running inside the state machine as if a database stored procedure.

## Client <-> Hosting actor <-> State machine actor workflow

The common request -> response work flow would be:

- Browser(client) send request to hosting node
- Hosting actor inside hosting node handles the reqeust.
- If the hosting actor sample-actor can response immediately, it will response directly back to the client. The request -> response loop ends.
- If the hosting actor cannot resposne immedaitely, it usually sends another txn to the state machine node. As the same time response a UUID back to the browser(client). The client will know it has to query the result at a later time using the UUID.
- The state machine node received the txn from hosting node. The txn is queued in the Proof of Time coveryor, sorted and eventually in-turn executed by the sample-txn-executor actor.
- The sample-txn-executor may change the state in the state machine in case of "command", or not change at all in the case of "query". The state machine will update the hosting node a result that any error or successfully.
- The client will periodically query the hosting node for the result of the previous request using the UUID.
- If the hosting has got the update from the state machine node, and update the result associated with the UUID, it will send back the resposne back to the client.
- The browser(client) will get a notification that the txn has been executed, with result either successful or error.

In this step, we will use SQL as an example to demo how this work flow works. All other type of reqeust will work the same way.

