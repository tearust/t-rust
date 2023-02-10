Dev portal is a system level TApp, made by TEA Core Team. Developers are the target users. 

Developers can use this Dev Portal:

* Create a TApp
* Deploy a TApp to testnet or mainnet
* Upgrade TApp 
* Review billing log

# Users are Devs

## Developers qualification

Anyone can be a developer. There is no special developer qualification or registration. So we will use the name "users or devs" to all users who think themselves developers and use this Dev Portal.

## Expense limit

The same as any other TApps, users need to set "expense limit" before using Dev Portal. All charges will be drawn from user layer 2 account within the expense limit.

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

The initial [TApp_status](TApp_status.md) is "pending", Because the developer has not uploaded and deploy any code yet.

We do not allow two TApp has the same name. So when create new TApp, a deduplication is needed. Return error when name is used by others.

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

The state codeis compiled into WebAssembly (wasm file). It has to be a single wasm file. 

 > 
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

### System actions

#### Query all TApps registered by this owner

Query and list on the UI. Including the TApps have existing code uploaded before.

#### Upload file and assign CID

After file uploaded to the current connected hosting node. It actually uploaded to IPFS. The CID is added to the SQL database. Also response to UI shown on the browser. The user need to write it down.

## Push the code across the network

The previous step only upload the code files to the connected host node. Only one node. In order to have other nodes hosts your app, you will need to "push" it all as much as possible nodes.

### User actions

User need to select a list of hosting nodes, click "push" to request those nodes to "load and register" the new uploaded back end actor code.

For state machines, users do not need to select. they are the same, can be considered a single entity.

The selected nodes need to show an indicator that the file has been pushed successfully. 

### System actions

#### Query hosting node if actor file loaded or not

UI needs an indicator to show if this actor file has been uploaded and registered by a hosting node.

#### Trigger the sync IPFS CID on hosting node

When user push, the host node will use IPFS to "pin" this CID. Once done, register actor. If success, update the SQL db mark "registered".

#### Query state machine node if actor file loaded or not

#### Trigger the state machine node to load and register actor file

This is a Txn sent from the connected hosting node. The txn request all the state machine nodes to load actor and register. If not cached in local IPFS, will find and "pin".

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
