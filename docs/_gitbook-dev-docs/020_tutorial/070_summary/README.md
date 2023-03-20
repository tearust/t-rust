# Summary

## Three parts of a typical TApp

We have gong through the entire process of building a TApp. The development contains three major projects.

* Front end,
* Hosting actor, as known as back end in web2 world,and 
* Txn executor, as known as stored procedure in web2 world.

The Front end is a SPA(Single page application). 

* It runs in browser (or as mobile client style in mobile devices)
* Its final built code is stored on IPFS. The CID will be the key for end users to access the TApp
* It send HTTP POST requests to the backend (hosting actor), and wait for responses. 
* It uses Metamask to identify user. Whoever can sign the login string will be recognized the authorized user.

The Hosting actor runs inside the hosting node. 

* It is compiled to a wasm file and loaded into hosting node.
* All logic in the wasm code is Lambda functions. It takes request and return response.
* It sends query or transactions to the Txn executor actor in state machine when query or change state. In our future version, a local state cache will be added to speed up the local query.
* Because all functions are Lambda, it doesn't store state, unless to call the TEA Project native provider. Always assuming the local state is vulnerable. The hosting node may be done or restart at any time.
* All function execution needs gas as fuel. The miner who run the hosting node gets paid by the gas fuel. More detail on the gas fee can be found in the Tokenomics white paper

The txn executor actor runs inside every state machine nodes.

* It is compiled to a wasm file. It is loaded into every state machine maintainer.
* All logic in the wasm file is Lambda functions. It takes txn parameters, and return result or modify state.
* All logic needs to be **deterministic**. No randomness or any I/O that may cause exception allowed.
* Txn executor can call  a limited list of native providers. 
* Txn executor write all state changes to the Context `ctx` object. At least, return the list of Contexts. The TEA Runtime will commit the changes to the state. 
* At any point of execution, when any error happens, the txn executor will return error without commit the state changes. The state will remain the same.
* The TEA runtime will sort all income txn based on their timestamps. So that all state machine maintainer nodes will execute at the same sequences. This is Proo of Time consensus we will discuss in the Technical white paper.

## Local development and local test

You are not supposed to run a prototype in the production environment because you are risk on real money. Deployment testnet won't need real money, but it takes some time and longer feedback loop. So the best way to test your business logic would be run it locally. 

Like Hardhat for Ethereum development, we have Dev-Runner for local simulator. There are still a few differences between the production and local Dev-Runner. For example, you may have an additional admin page to trigger initiliazation step. This step is part of the Developer Portal in the production. Also, you can use **faucet** to get free test token, this is not possible in the production.

## Deployment

TODO://

## Gas fee

TODO://

## What's the next

There are many simplification or placeholders in our tutorial-v1, such as:

* The task title is unique value. If someone created "Buy me a beer", no one else can create a task with the same title later.
* The creator need to be honest when verify the worker's work. We need a "secured oracle" to determine if the task is completed successfully without rely on creator's honesty.
* There is no time limit. The worker can take a task and never complete
