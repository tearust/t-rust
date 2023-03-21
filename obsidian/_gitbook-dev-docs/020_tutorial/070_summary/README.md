# Summary
## Three parts of a typical TApp

We have gone through the entire process of building a TApp. The development contains three major projects.

- Front end.
- Hosting actor, also known as back end in the web2 world. 
- Txn executor, also known as stored procedure in the web2 world.

The Front end is a SPA(Single page application). 

- It runs in the browser (or as a mobile-style client in mobile devices).
- Its final built code is stored on IPFS. The CID will be the key for end users to access the TApp.
- It sends HTTP POST requests to the backend (hosting actor), and waits for responses. 
- It uses Metamask to identify the user. Whoever can sign the login string will be recognized as the authorized user.

The Hosting actor runs inside the hosting node. 

- It's compiled to a wasm file and loaded into a hosting node.
- All logic in the wasm code are Lambda functions. It takes requests and returns responses.
- It sends queries or transactions to the Txn executor actor in the state machine whenever there's a query or request to change the state, respectively. In our future version, a local state cache will be added to speed up the local query.
- Because all functions are Lambda, they don't store the state, unless to call the TEA Project native provider. Always assume the local state is vulnerable. The hosting node may be done or restart at any time.
- All function execution needs gas as fuel. The miner who runs the hosting node gets paid by the paid gas fuel. More details on the gas fee can be found in the Tokenomics section of our white paper.

The txn executor actor runs inside of every state machine node.

- It's compiled to a wasm file and loaded into every state machine maintainer.
- All logic in the wasm file are Lambda functions. It takes txn parameters, and returns the result or modifies the state.
- All logic needs to be **deterministic**. No randomness or any I/O that may cause exceptions is allowed.
- The txn executor can call  a limited list of native providers. 
- The txn executor writes all state changes to the Context `ctx` object. At minimum, it'll return the list of Contexts. The TEA Runtime will commit the changes to the state. 
- At any point of execution, when any error happens, the txn executor will return the error without commiting any state changes (i.e. the state will remain the same).
- The TEA runtime will sort all incoming txns based on their timestamps. So that all state machine maintainer nodes will execute on the same sequences. This is the Proof of Time consensus we discussed in the white paper.

## Local development and local test

You're not supposed to run a prototype in the production environment because you're risking real money. Deploying on testnet won't need real money, but it takes some time and has a longer feedback loop. So the best way to test your business logic would be to run it locally. 

Like Hardhat for Ethereum development, we have Dev-Runner to use as a local simulator. There are still a few differences between the production and local Dev-Runner. For example, you may have an additional admin page to trigger the initiliazation step. This step is part of the Developer Portal in the production environment. Also, you can use a **faucet** to get free test tokens, which isn't possible in the production environment.

## Deployment 

TODO://

## Gas fee

TODO://![[Pasted image 20230320085836.png]]

## What's next

There are many simplification or placeholders in our tutorial-v1, such as:
- The developer has not get paid yet.
- The task title is a unique value. If someone created "Buy me a beer", no one else can create a task with the same title later.
- The creator needs to be honest when verifying the worker's work. We would need a "secure oracle" to determine if the task is completed successfully without relying on creator's honesty.
- There's no time limit. The worker can take a task and never complete it.

