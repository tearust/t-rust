Linksto other articles.

[[Developer_requirement]]


Dev portal is a system level TApp, made by TEA Core Team. Developers are the target users. 


Developers can use this Dev Portal
- Create a TApp
- Deploy a TApp to testnet or mainnet
- Upgrade TApp 
- Review billing log

# Users are Devs

## Developers qualification

Anyone can be a developer. There is no special developer qualification or registration. So we will use the name "users or devs" to all users who think themselves developers and use this Dev Portal.

## Expense limit

The same as any other TApps, users need to set "expense limit" before using Dev Portal. All charges will be drawn from user layer 2 account within the expense limit.

# Create a TApp

## User action

User click "Add TApp" button.
Input the following information:
- App name (System need to deduplicate from existing names)
- Description
- Owner address (Disabled and fixed to this user H160 address)
- Front end CID (Empty and disabled now)
- Back end actor CID (Empty and disabled now)
- State actor CID (Empty and disabled now)
- Status (disabled, default is Pending)

## System action
Create this TApp entry in the TAppStore database.

The initial [[TApp_status]] is "pending", Because the developer has not uploaded and deploy any code yet.

We do not allow two TApp has the same name. So when create new TApp, a deduplication is needed. Return error when name is used by others.

# Deploy a TApp
Deploy is a completed workflow. It contains the following steps:
- Build, compile code
- Upload code with a version stamp to Dev Portal
- Push newly uploaded files to hosting nodes and state machines
- Wait and confirm all requested hosts and state machines have the files loaded
- Start the App

## Build, compile code

The users have to build the front end, backend, state, all three tiers code offline.

The front end code is js/html/css, a SPA (Single Page Application). All the code compressed to a package then upload to Dev Portal.

The back end code is complled into WebAseembly (wasm file). In most cases, it is  single wasm file. It will be uploaded to Dev Portal. We will explain shortly.

The state codeis compiled into WebAssembly (wasm file). It has to be a single wasm file. 

> FAQ: Where are they supposed to be run?
> Front end code runs inside end user browser. 
> Back end code runs inside the enclave of Hosting node (Miners own)
> State code runs inside the enclave of all State Machine (Statemachine maintainers own)


## Upload code 

### User actions

User can see a list of all TApps this user registered as the owner.
User select the App he wants to upload code. Note, this can also be used as update if this app has code previously uploaded already. For detail about upgrade, check the later upgrade section.

Three upload fields: Front end, Back end, State actor. Every file need to assign a version number. The version number need to deduplicated. 

When upload completed, the CID show on the UI.  

Also consider the [[hybrid_storage_solution]] to reduce the dependency of IPFS.

### System actions

#### Query all TApps registered by this owner

Query and list on the UI. Including the TApps have existing code uploaded before.

#### Upload file and assign CID
After file uploaded to the current connected hosting node. It actually uploaded to IPFS. The CID is added to the SQL database. Also response to UI shown on the browser. The user need to write it down.

Also consider the [[hybrid_storage_solution]] to reduce the dependency of IPFS.

## Push the code across the network

The previous step only upload the code files to the connected host node. Only one node. In order to have other nodes hosts your app, you will need to "push" it all as much as possible nodes.

### User actions

User need to select a list of hosting nodes, click "push" to request those nodes to "load and register" the new uploaded back end actor code.

For state machines, users do not need to select. they are the same, can be considered a single entity.

The selected nodes need to show an indicator that the file has been pushed successfully. 

### System actions

#### Trigger the sync IPFS CID on hosting node

When user push, the host node will use IPFS to "pin" this CID. Once done, register actor. If success, the host node update the list of registered actors locally. this will be used when query.

This is a hosting node handler. It is async. It will not return download completed immediately. The result of uploading status need a separated query.

#### Query hosting node if actor file loaded or not

UI needs an indicator to show if this actor file has been uploaded and registered by a hosting node.

A hosting node has internal register actor list in memory. 

#### Trigger the state machine node to load and register actor file

This is a Txn sent from the connected hosting node. The txn request all the state machine nodes to load actor and register. If not cached in local IPFS, will find and "pin".

This is a regular txn called "Register_Actor". All state machine nodes will execute this txn at the same time.

A state machine node will start the download and register flow, but this txn is async. It will not return the download result immediately. But return an acknowledgement. The progress of result will be queried in later txn. So this txn execution will always be consensus between all nodes.

#### Query state machine node the status of register an actor

The user can trigger a query txn from front end. Or this query can be down automatically from the front end.

This is a standard txn that hosting node send to state machine nodes.

All state machine nodes will execute the same txn. During execution, it check local registered actor list, if successfully downloaded and registered, it will generate a result. This result will be send to a "preselected" state machine node.

This preselected node is the same logic as Signing a ETH transation at withdraw.

So this selected node will gather the results from all other state machine nodes. it aggregate the result and get back to the caller (the hosting node).

The hosting node receive the result then return to front end.

The result should look like "6 of 10 state machines have successfully registered" at the front end.

When all state machine have successful, the "start tapp" will be available to the next step.

Question: What if a few state machine cannot successfully download and registered eventually?

Solution: Run a kick out process to remove the failed state machine nodes after a long-enought grace period.




#### Start tapp

First check if all requested hostign node and all state machine nodes have all load & registered, otherwise reject return error

If all checked OK, return success. Mark the app "Active" status in TAppStore.

# Upgrade TApp

Upgrade is the same workflow as the Upload step inside Deploy TApp section. But with a few additional options

## Set TApp Upgrading status and Resume

Some upgrade cannot be done smoothly without stop service. The owner need to stop for upgrade, then resume afterwards

When TApp in Upgrade status, it will stop accept user input until resume.

Resume is an action set the TApp status back to Active.

## Remove a TApp

The owner decided to remove the tApp. 

Delete the App. Deregister actor.


## Adjust billing /pricing 

Adjust billing related properties.

# Review billing log
TODO