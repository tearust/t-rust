Links to other articles.

[Developer_requirement](Developer_requirement.md) -> published at *t-rust/docs/_gitbook-dev-docs/1_tutorial/Developer_requirements*
*t-rust/obsidian/dev_portal_design/Hosting_actor_handlers*
[State_actor_handlers](State_actor_handlers.md)

Dev portal is a system level TApp, made by TEA Core Team. Developers are the target users. 

Developers can use this Dev Portal

* Create a TApp
* Deploy a TApp to testnet or mainnet
* Upgrade TApp 
* Review billing log

# Users are Devs

## Developers qualification

Anyone can be a developer. There's no special developer qualification or registration. So we will use the name "users or devs" to all users who think themselves developers and use this Dev Portal.

## Expense limit

The same as any other TApps, users need to set "expense limit" before using Dev Portal. All charges will be drawn from user's layer 2 account within the expense limit.

# Create a TApp

## User action

User click "Add TApp" button.
Input the following information:

* App name (System need to deduplicate from existing names)
* Description
* Owner address (Disabled and fixed to this user H160 address)
* Front end CID (Empty and disabled now)
* Back end actor CID (Empty and disabled now)
* State actor CID (Empty and disabled now)
* Status (disabled, default is Pending)

## System action

Create this TApp entry in the TAppStore database.

The initial [TApp_status](TApp_status.md) is "pending", Because the developer has not uploaded and deployed any code yet.

We don't allow two TApps to have the same name. So when a new TApp is created, a deduplication is needed. An error is returned when the name is already in use.

# Deploy a TApp

Deploy is a completed workflow. It contains the following steps:

* Build, compile code
* Upload code with a version stamp to Dev Portal
* Push newly uploaded files to hosting nodes and state machines
* Wait and confirm all requested hosts and state machines have the files loaded
* Start the App

## Build, compile code

The users have to build the front end, backend, state, all three tiers code offline.

The front end code is js/html/css, a SPA (Single Page Application). All the code compressed to a package then upload to Dev Portal.

The back end code is complled into WebAseembly (wasm file). In most cases, it is  single wasm file. It will be uploaded to Dev Portal. We will explain shortly.

The state code is compiled into WebAssembly (wasm file). It has to be a single wasm file. 

 > 
 > FAQ: Where are they supposed to be run?
 > Front end code runs inside end user's browser. 
 > Back end code runs inside the enclave of Hosting node(s) (Miners own)
 > State code runs inside the enclave of all State Machines (State machine maintainers own)

## Upload code

### User actions

User can see a list of all TApps this user has registered as the owner.
User selects the TApp he wants to upload code. Note, this can also be used as update if this app has code previously uploaded already. For details about upgrade, check the latest upgrade section.

Three upload fields: Front end, Back end, State actor. Every file need to assign a version number. The version number need to deduplicated. 

When upload completed, the CID is shown on the UI.

Also consider the [hybrid_storage_solution](hybrid_storage_solution.md) to reduce the dependency of IPFS.

### System actions

#### Query all TApps registered by this owner

Query and list on the UI. Including the TApps have existing code uploaded before.

#### Upload file and assign CID

After file is uploaded to the current connected hosting node, it's actually uploaded to IPFS. The CID is added to the SQL database. Also response to UI shown in the browser. The user needs to write it down.

Also consider the [hybrid_storage_solution](hybrid_storage_solution.md) to reduce the dependency on IPFS.

## Push the code across the network

The previous step only uploads the code files to the connected host node. Only one node. In order to have other nodes hosts your app, you will need to "push" it all as much as possible to a wider range of nodes.

### User actions

User needs to select a list of hosting nodes and click "push" to request those nodes to "load and register" the newly uploaded back end actor code.

For state machines, users don't need to select. They're the same and can be considered a single entity.

The selected nodes need to show an indicator that the file has been pushed successfully. 

### System actions

#### Trigger the sync IPFS CID on hosting node

When user pushes, the host node will use IPFS to "pin" this CID. Once done, the actor is registered. If successful, the host node updates the list of registered actors locally. This list will be used whenever there's a query.

This is a hosting node handler. It is async. It won't return status "download completed" immediately. The result of uploading status needs a separate query.

#### Query hosting node if actor file loaded or not

UI needs an indicator to show if this actor file has been uploaded and registered by a hosting node.

A hosting node has an internal register actor list in memory. 

#### Trigger the state machine node to load and register actor file

This is a Txn sent from the connected hosting node. The txn request all the state machine nodes to load actor and register. If not cached in local IPFS, will find and "pin".

This is a regular txn called "Register_Actor". All state machine nodes will execute this txn at the same time.

A state machine node will start the download and register flow, but this txn is async. It will not return the download result immediately. But return an acknowledgement. The progress of result will be queried in later txn. So this txn execution will always be consensus between all nodes.

#### Query state machine node the status of register an actor

The user can trigger a query txn from front end. Or this query can be down automatically from the front end.

This is a standard txn that hosting node sends to state machine nodes.

All state machine nodes will execute the same txn. During execution, it checks the local registered actor list. If successfully downloaded and registered, it will generate a result. This result will be sent to a "preselected" state machine node.

This preselected node is the same logic as Signing an ETH transaction at the time of withdrawal.

So this selected node will gather the results from all other state machine nodes. It aggregates the result and reports back to the caller (the hosting node).

The hosting node receives the result then returns it to the front end.

The result should look like "6 of 10 state machines have successfully registered" as seen in the front end.

When all state machines have successfully registered, the "start tapp" will be available to the next step.

Question: What if a few state machines cannot successfully download and register eventually?

Solution: Run a kick out process to remove the failed state machine nodes after a long-enough grace period.

#### Start tapp

First check if all requested hosting node and all state machine nodes have all loaded & registered, otherwise reject and return an error.

If all checked OK, return success. Mark the app "Active" status in TAppStore.

# Upgrade TApp

Upgrade is the same workflow as the Upload step inside Deploy TApp section. But with a few additional options.

## Set TApp Upgrading status and Resume

Some upgrade cannot be done smoothly without stopping service. The owner needs to stop for upgrade, then resume afterwards.

When TApp is in Upgrade status, it will stop accepting user input until resume.

Resume is an action set the TApp status back to Active.

## Remove a TApp

The owner decided to remove the tApp. 

Delete the App. Deregister actor.

## Adjust billing /pricing

Adjust billing related properties.

# Review billing log

TODO
