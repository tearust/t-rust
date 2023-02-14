## Running inside state machine

If you are a web 2 developer, you may be familiar with a concept "Stored procedure". Usually it is running inside database server. Back end will remotely call this "Stored procedure" to get a more complex logic that happens inside the database server.

The state machines can be consider the database tier. However it is more than a database, it is a distributed state machine with a GlueSQL embedded inside.

## Use state actor only when necessary

In many simple cases, you do not need to write a state actor. All you need can be implemented by either calling a TAppStore API, or send a SQL scripts to GlueSQL. But if the logic is complex, especially it is a multiple steps logic, writing a State Actor might be your only choice.

Please read the detail of how the distributed state machine runs here PLEASE_ADD_THE_STATE_MACHINE_DOCUMENT_LINK_HERE. Briefly you should know the following concepts:

* The state machine consensus algorithm guarantees all active state machine nodes are in the same state all the time.
* Your request (also called txn) will be queued and executed at a deterministic order in all state machine nodes.
* Txns are executed one after another. No parallel execution for  txns in state machine. So you can assume your code runs in exclusive single thread mode that no need of thread lock during execution. 
* If an mutable txn executed successfully, the state version (called timestamp, TS) will increase to the TS of this txn. If it fails, nothing will change in the state.

Because the state actor code running inside every state machine node synchronizely, you can easily look up values in the state machine. Base on this value go different follow up logic. This is very convinent because you cannot get the state value synchronizely when your logic was inside a hosting node actor. Because your host node will not have the latest / current state, your code is not executed exclusively. You have to assume other hosting actor runs some code to change the value of the state at the same time. This is much more complex logic and it is the cost of decentralization. This explains why you need the state actor beyond the hosting node actor. 

Every advantage has a cost. For the state actor function call, the cost would be:

* Hosting actor send txn to be executed in State maintainer nodes, there is network letency
* The state machine maintainers need to put all txns waiting a so-called Conveyor to compensate the inconsistent network legancy to all state machine nodes.
* Your code will run in all state machine maintainer nodes. The gas consumption is much larger

So you have known only use State actor when necessary.

## Understand memory tax and state storage

State machine is where you store the state of your application.. But it is not the only place you can store your data. Click this link to read the comparasion of all storage solutions PUT_LINK_TO_OUR_ORIGINAL_STORAGE_COMPARISON. Among them, state storage is the most expensive, because of memory tax.

All TApps share the single state, so that they can transfer assets between them. Because the state is a shared limited public resource, the prevent abuse is very important when designning the tokenomics. We use Memory Tax to prevent such abuse. The more memory you use in the state the more cost the tApp need to pay, the longer you store the more you pay. The tax rate is not fixed. The less available memory left, the higher the tax rate. To get more detail about memory tax, please go to PUT_OUR_MEM_TAX_DOCUMENT_LINK_HERE.

Note the Memory tax is not implemented in Epoch13 yet. 
