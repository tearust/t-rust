# A deep dive into gas measurement and settlement

## How is the gas fee measured for my TApp?

As a TApp developer, you're writing wasm actor code. Your code will be compiled into a wasm binary file and then loaded into the TEA runtime to execute. The TEA runtime is based on [wasmer](https://wasmer.io/) which can measure computing consumption during code execution. 

## How is gas fee measured or estimated for Native Actor Functions?

Your code may call other functions provided by the tea-sdk. Most of those functions are **Native actor functions**. They're not wasm code but native code. There's no way for wasmer to measure the gas fee of native code. So the TEA runtime will give every native function an "estimate price" based on  calibration. We'll release a price list when the TEA Project launches into production release. 

Your input arguments for the native function call may significantly affect the gas consumption, so there's an **estimate** function for every native function. This estimate will be used for every native call before actual execution. In case the estimate is over the gas limit, the function will not be executed, and will return a "gas limit overflow" error.

## What is the gas limit, and what happens if it's exceeded?

The gas limit is borrowed from Ethereum. It's used to protect the consumer in case of any unforeseeable situations (software bugs such as an infinite loop, garbage input data etc. ) which causes an extremely huge gas bill. It's also used to prevent DDoS attacks. 

All operations will carry a gas limit with the function call. The gas consumption is measured and deducted in real time. As soon as the gas limit runs out, the execution will be terminated immediately. The caller will receive an error of Over Gas Limit. 

Because the native function call cannot be terminiated during execution, an estimation function will be called before execution. If the gas limit cannot cover that estimation, the execution will be terminated before actually called.

During our test phase, we set a 1 TEA gas limit for all operations which should be large enough. In the future, we'll allow developers or end-users to set their own gas limits for any specific operations that consumes a lot of gas.

## When is the gas settlement?

Although the gas limit is measured and deducted in real time, the payment of the gas fee from the end users to the miners is not settled in real time to reduce the system load. The bill will be accumulated and settled after a period of time. This time is called the Billing cycle.

In our local dev-runner, it's set to one minute just so that we can get the gas bill logged sooner. In our testnet, it's set to 10 minutes. Likely we'll set a 10 minute billing cycle in our production envrionment, but it may change in the future.

There's a chance that the payer cannot pay the gas bill in full at the time of settlement. This is considered bad debt which is unavoidable and in most cases acceptable. We'll measure the bad debt ratio in our future production environment. Based on that ratio, we may decide to increase or decrease the  gas limit restriction. It's always a trade off between bad debt and cost of the system load.

## Other costs or profits from developers point of view

The gas fee is paid by the enduser to the miners. For developers there are other profits or costs:

* Memory tax: Paid to the state machine for occupying memory in the state (or SQL).
* Txn fee to the state machine: Paid to the state machine for executing the txn-executor.
* Gas subsidy for end users: Developer can cover the gas fee originally payable by the end user, e.g. a developer could subsidize gas to promote usage of their app. Thus the end user can use the TApp for free.
* In-app purchase: End user pays the developer (the TApp). This is the direct income to the TApp.

For more detail, please go to the tokenomics section of the TEA Project's white paper.

## Reduce the gas fee and memory tax

If you know how to reduce the gas cost in Ethereum, all those rules will also mostly work in the TEA Project. Additionally, you should also consider how to reduce the memory tax, which doesn't existing in Ethereum. The details of the Memory Tax is beyond the scope of this tutorial. We can list a few items to consider but more details will be covered in our developer documents and in our future Tutorial-v2.

* Delete records in SQL instead of simply "mark-as-deleted". SQL data lives inside the memory of all state machine enclaves. Memory is an expensive resource.
* Only store relationships to SQL and store all blob data to OrbitDB. The blob storage is beyond the scope of this tutorial. We'll cover it more in our tutorial-v2.
* When possible, store the query result locally using the Native actor's "keyvalue pair provider". This is a cache that can significantly reduce queries to the database. In our future version, a local state cache can allow you to almost exclusively perform local queries.
* When possible, combine smaller txns into one larger txn. 
* If there's a native actor function that can do something, never rewrite your own wasm version. Wasm code is always more expensive than Native code.
* If you know of another developer's actor that's widely used for some feature, please call them instead of rewriting your own. In most cases, those widely used popular actors are optmized to save costs compared to your newly written one.
* Run your code locally using dev-runner and check the log to see how much gas it consumes. This "dry run" can save you and your customers a lot of money before testing in the real production environment.
