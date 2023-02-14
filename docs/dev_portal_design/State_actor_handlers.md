## Running inside state machine

If you're a web2 developer, you may be familiar with the concept of "Stored procedure". Usually it's running inside database server. Back end will remotely call this "Stored procedure" to get more complex logic that happens inside the database server.

The state machines can be considered to be the database tier. However it's more than a database, it's a distributed state machine with GlueSQL embedded inside.

## Use state actor only when necessary

In many simple cases, you don't need to write a state actor. All you need can be implemented by either calling a TAppStore API, or send a SQL script to GlueSQL. But if the logic is complex, especially if it's multiple step logic, writing a State Actor might be your only choice.

Please read the detail of how the distributed state machine runs here PLEASE_ADD_THE_STATE_MACHINE_DOCUMENT_LINK_HERE. You should have a good general overview of the following concepts:

* The state machine consensus algorithm guarantees all active state machine nodes are in the same state all of the time.
* Your request (also called txn) will be queued and executed at a deterministic order in all state machine nodes.
* Txns are executed one after another, i.e. there are no parallel execution of  txns in the state machine. So you can assume your code runs in exclusive single thread mode without needing thread lock during execution. 
* If a mutable txn executed successfully, the state version (called timestamp, TS) will increase to the TS of this txn. If it fails, nothing will change in the state.

Because the state actor code running inside every state machine node runs synchronously, you can easily look up values in the state machine. Your follow up logic can therefore change based on the state value. This is very convenient because you cannot get the state value synchronously when your logic is inside a hosting node actor. Because your host node will not have the latest / current state, your code is not executed exclusively. You have to assume another hosting actor is running some code to change the value of the state at the same time. This is much more complex logic and it's the cost of decentralization. This explains why you need the state actor beyond the hosting node actor. 

Every advantage has a cost. For the state actor function call, the cost would be:

* Hosting actor sends txn to be executed in State maintainer nodes, which introduces network latency.
* The state machine maintainers need to put all txns in a queue on a so-called Conveyor to compensate for the inconsistent network latency of all state machine nodes.
* Your code will run in all state machine maintainer nodes. The gas consumption is much higher.

Based on the above factors, you should only use the State actor when necessary.

## Understanding memory tax and state storage

The state machine is where you store the state of your application. But it's not the only place you can store your data. Click this link to read the comparison of all storage solutions PUT_LINK_TO_OUR_ORIGINAL_STORAGE_COMPARISON. Among them, state storage is the most expensive, because of the memory tax.

All TApps share the single state, so that they can transfer assets between them. Because the state is a shared limited public resource, preventing abuse is very important when designning the tokenomics. We use Memory Tax to prevent such abuses. The more memory you use in the state, the more your TApp will need to pay. And the longer you store in the state machine, the more you'll pay. The tax rate is not fixed. The less available memory is left, the higher the tax rate. To get more details about the memory tax, please go to PUT_OUR_MEM_TAX_DOCUMENT_LINK_HERE.

Note that the Memory tax is not implemented in Epoch13 yet. 
