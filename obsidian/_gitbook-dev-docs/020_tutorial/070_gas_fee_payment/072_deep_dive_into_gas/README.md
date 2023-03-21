# A deep dive into gas measurement and settlement

## How does gas fee measured for my TApp?

As a TApp developer, you are writing wasm actor code. Your code will be compiled into wasm binary then loaded into the TEA runtime to execute. The TEA runtime is based on wasmer(https://wasmer.io/) that can measure the computing consumption during code execution. 

## How does gas fee measured or estimated for Native Actor Functions?

Your code may call other function provided by tea-sdk. Most of those functions are **Native actor function**. They are not wasm code but native code. There is no way for wasmer to measure the gas fee of native code. So the TEA runtime will give every native function a "estimate price" based on  caliberation. We will release a price list when TEA Project production release. 

You input arguments of the native function call may significantly affect the gas consumption, so there is a **estimate** function for every native function. This estimate will be used for every native call before actual execution. In case of the estimation over the gas limit, the function will not be executed, and return gas limit overflow error.

## What is gas limit, what if over the limit?

The gas limit is borrowed from Ethereum. It is used to protect the consumer in case of any unforeseeable situation (software bug such as infinity loop, garbage input data etc ) cause extreme huge gas bill. It is also used to prevent dDoS attacks. 

All operation will carry a gas limit with the function call. The gas consumption is measured and deducted at realtime. As long as the gas limit runs out, the exeuction will be terminited immediately. The caller will receive an error of Over Gas Limit. 

Because the native function call cannot be terminiated during execution, an estimation function will be called before execution. If the gas limit cannot cover that estimation, the exeuction will be terminited before actually called.

During our test phase, we set a 1 TEA gas limit for all operations. It should be large enough. In the future, we will allow developers or end users to set their own gas limit for any specific operation that consume a lot of gas.

## What is settlement? When the gas payment is settled?

Although the gas limit is measured and deducted in the real time, the payment of gas fee from the end users to the miners are not settled in real time to reduce the system load. The bill will be accuminated and settled after a period of time. This time is called Billing cycle.

In our local dev-runner, it is set to one minute, so that we can get the gas bill log sooner. In our test net, it is set to 10 minutes. Likely we will set 10 minutes billing cycle in our production envrionment, but it may change in the future.

There is a chance that the payer cannot pay the gas bill in full at the time of settlement. This is bad debt. This is not avoidable and in most cases acceptable. We will measure the bad debt ratio in our future production envionrment. Base on that ratio, we may decide to increase or decrease the restriction of gas limit. It is always a trade off between bad debt and cost of system load.

## Other cost or profit from developers point of view

Gas fee is paid by the end user to the miners. For developers there are other profit or cost:
- Memory tax: Pay to the state machine for occupying memory in the state (or SQL)
- Txn fee to the state machine: Pay to the state machine for execute the txn-executor 
- Good Santa for end users: We may have a better name later. Developer can cover the gas fee originally payable by the end user. E.g. advertisement. Thus the end user can use for free.
- In-app purchase: End user pay to the developer (the TApp). This is the directly income to the TApp.

For more detail, please go to Tokenomics White Paper of TEA Project.

## Reduce the gas fee and memory tax

If you know how to reduce the gas cost of Ethereum, all those rules are mostly work in TEA Project. Additional, you should also consider how to reduce the memory tax, which is not existing in Ethereum. The detail of Memory Tax has beyond the scope of this tutorial. We can list a few items to consider. More detail will be covered in our Developer document and future Tutorial-v2.

- Delete record in SQL instead of simply "mark-as-deleted". SQL data lives inside the memory of all statemachine enclaves. It is an expensive resource.
- Only store relationship to SQL. All blob data store to OrbitDB. The blob storage is beyond the scope of this tutorial. We will cover in our tutorial-v2
- When possible store query result locally using Native actor "keyvalue pair provider". This is a cache that can significant reduce the query to the database. In our future version, a local state cache can allow you to query mostly locally.
- When possible, combine smaller txns into one larger txn. 
- If there is a native actor function can do something, never rewrite your own wasm version. Wasm code is always more expensive than Native code.
- If you know some other developers have an actor widely used for some feature, please call them instead of rewrite your own. In most cases, those widely used popular actors are optmized to save cost then your new written one.
- Run your code locally using dev-runner, check the log to see how much gas it consumes. This "dry run" can save you and your customer a lot of money before test in the real production environment.

