# Definations

* Client: The DAPI server that starts a task to TEA Project
* Delegator: One of the TEA Project's hosting node that selected by the Client. It is the only node that talks to the Client. 
* Executor: The hidden hosting node that actually execute the code. Executing the code means send the http API call to a bank.
* State maintainer: This is the TEA Project state machine nodes. It works as dispatcher. They select an Executor. The selecction is random, verifiable, non-predictable, secure.
* Actor: The wasm code that runs inside TEA Project. It could be written by DAPi.

# Deployment steps

* Developer upload Rust (or any other languages that can compile to wasm, follow the TEA Project SDK standard) code to TEA Project Developer Portal (WIP).
* One enclave (in hosting node) win a chance to compile this source code to wasm actor binary (WIP)
* The wasm binary is stored in the IPFS. The CID is the handle of this function. The developer id (address), source code hash, and the wasm binary hash are recorded. 
* Developer is the owner of this binary. Developer starts this actor as a micro service (we used to call it an Errand)
* All Executor nodes (who is eligable to run this Errand) will be ready to execute this actor as long as the State assign it such a task.

# Execution steps

* When an Executor node (one of the hosting nodes) starts, it will connect to any one of State maintainer node. Ping the state frequently for future execution opportunity. This is a one-time startup process. As long as the hosting node alive, it is always on the standby mode.
* Client connect to Ethereum (or any TEA supported layer1 blockchain. It could be whatever DAPI is using).
* Client call the Ethereum TEA smart contract API to get a list of hosting nodes that currently host DAPI actors.
* Client randomly select a hosting node as Delegator.
* Client login to this Delegator. Receive a session key which is valid for 60 minutes
* Client send http call to this Delegator. this call include the session key and every thing the  DAPI call needed.
* Delegator generate a TEA Project CMD to the State maintainer
* State maintainer execute this CMD. This CMD select (randomly) an Executor who will later run this DAPI call.
* If necessary, a group of validator (Remote attestators) can be sleccted too. Their job is to monitor the Executor doing the task. We may discuss how the remote attestation monitoring work in a seperated article 
* The Executor pings the state. The executor receive a notification from one of the State maintainer (any one, they are identical). Executor get the DAPI actor detail
* The Executor run the actor code in enclave. The execution is monitored by the Remote Attestators.
* While executing, this Executor call the bank API based on what the actor code is written
* The Executor receives the response from the bank.
* The Executor forward the response back to the State maintainer in the 2nd CMD.
* The State maintainer execute the 2nd CMD to pay the bill and send result back to the Delegator
* The Delegator send back the response to the Client
* The Client receives the result of DAPI call.

# Features

## Randomness

The State maintainer can randomly select Executor. The selection and who was selected all happens inside and between enclaves. There is no way to know from outside world

## Security

All execution happens inside enclave. There is no way to know from outside world

## Privacy (No admin access)

\** TEA Project's goal is to have everything out of human control\*\*. No one, including the developer and system admin has no access to the encalve. In other word,\*\* no one know what actually happened inside the encclave. **

## Non-stoppable

Executor is randomly selected. Unless all hosting nodes are banned by the bank, there is no way to stop the API call.

## Billing

DAPI call is a regular TEA tasks. The Client will need to pay all the cost. The cost are:

* All gas fee inside Executor, Delegator and State maintainer
* Memory tax (if the DAPI actor needs  to store anything in the TEA Project state memory)

The Client needs to have a TEA account first. The billing system will deduct the gas and tax from this client's TEA account. Bill is paid in TEA.
