This article are for internal use only. Do not share the link to public directly.

The comparision with Phala Network
# Disclaimer
Because Tea project is still under heavy development, all the features we are talking in this article may or may not completed.
 
# Features in common
## Hardware protected enclave
Instead of using cryptographic algorithm to protect privacy, both projects use hardware enclave. Phala uses CPU based TEE, TEA Project uses TPM based trusted computing.
## Help protect privacy and trustedless code execution
Code and data running inside both enclaves are protected. Human (including the miners or the admin of the system) cannot access to the enclave. This is different than the traditional cloud computing (admin may access to the machine) and traditional blockchain (everything is publicly verifable)
# Differences
## Project stage
Phala has mainnet running for a while. Tea project is still under heavy development. In this case, Phala is mature. 
## TEE vs Trusted computing
TEE is a CPU technology, such as Intel SGX. It generate an enclave inside CPU hardware. Trusted computing is a security chip technology. It does not generate enclave inside the TPM chip, instead use TPM as "roof of trust" to protect a much larger system, such as a full node or partial node. 

TEE relies on the CPU manufacturor validation service. TPM is more general since there are too many TPM manufacturors then CPU.

TEE is easy to develop because the CPU venture has ready-to-use SDK but Trusted computing only provide very limited low-level protocol (TCG2.0), there are a lot to be done to use TPM chips in any real cases. That is why TPM is mostly used in Operation Systems (Micrsoft, Apple, Google, etc) instead of end-user facing applications.

TEE has a much smaller execution environment becauase it is insdie a CPU. The capability is limited by the CPU too. TPM doesn't execute code inside TPM chip itself, it just provide a "root of trust", the execution happens in the whole computing. There is no limit on the size or capacity. Due to the same reason, protecting the larger environment (TEA)is harder than smaller CPU (TEE). 

TEE is vendor locked. Some vendor (eg, Intel) has stopped shipping SGX CPU to consumer market. This can cause a new kind of centralization - only data center can use this technology.  TPM chip is low technology. They all follow the same TCG2.0 standard. The TPM market is very decentralized, not likely to be monoply by a few larger vendors like CPU market.

## A blockchain or not a blockchain
Phala is a blockchain project. It has its own Substrate based DOT parachain. Tea Project is NOT a blockchain. Tea Project uses the layer1 as one of the three roots of trust. Tea Project layer1 can be a Substrate based blockchain (for version epoch10 and earlier) or a pure smart contract (for version epoch11 or later). Since the layer1 can be just a smart contract, it can be running on any Turing Complete blockchain, such as Ethereum, Polkadot, BSC,NEAR...). There is NO applicaiton running on layer1, all Tea Applications are running on the layer2. The layer2 is NOT a blockchain. It is a decentralized state machine. There is no block, there is no chain. 

## Hardware requirement
Phala node requires a TEE ready CPU. Tea project node requires a TPM chip on board and a GPS receiver. The GPS receiver is very important because Tea Project uses timestamp from the GPS sats to reach consensus of the state machine. It is called "proof of time".

## Smart contract, Fat contract vs General purpose computing
Phala introduces an enhanced verison of smart contract, it is called "Fat Contract". It can do much more than a regular smart contract. But it is still a task-based function, similar to the Lambda in cloud computing.

Tea application is not a smart contract nor fat contract. It is general purpose computing, similar to the micro services in cloud computing. Tea applicaitons are 3-tiers architecture. the miners can host an application as if it hosts a web services. 

## State stored in blockchain or decentralized state machine
Phala fat contract store state back to the caller. The state stored in the blockchain. That is why I said Phala is still a blockchain.

Applicaiton can store their app state into the layer2 state machine as if they are a database. The application state do NOT stored in blockchain. That is why Tea applicaiton can use SQL scripts in their applicaiton because they can assume there is a SQL database running at the backend. So Tea Project is not a blockchain.

Due to the same reason, Tea project has no concepts called "TPS". Its state machine is a continue updating database. There is no block size limit, no block verification interval. 

## Scalability and limitations
Phala runs each fat contract in separated TEE nodes, there is no need to run consensus between them. It can reach a far higher scalability than traditional blockchain. The only limitataion may come from the blockchain layer. Because it is still a blockchain, the state is stored in its blockchain. If there are too many tasks running simultanously, the blockchain may be blocked.

Tea project is not a blockchain, it is more like a distributed database. There is no block size limit or block interval. The database is continuous updating. The only limit would be the processing power and network limit. Technically it can run as fast as today's cloud computing.

## Clients: Computing oracles or hosting platform
Phala is designed to work as a computing oracle. So that other parachain can off load their heavy computing smart contract to Phala. The result will go back to the original parachain (caller). In this case, phala is a backend-as-a-service model. End user would be the user of that parachain.

Tea project can do the same backend-as-a-service model as Phala does, it also has a "hosting tApps" mode that allow the application to be hosted on the platform. This is similar to cloud computing hosting service but in decentralized way. Because of the decentralized nature, the Tea applications are
- Non stoppable
- Non breakable
- Censorship free
Similar to IPFS but not just hosting data storage, but computing and database.

## Tokenomics: Gas on computing or memory tax

Phala provides computing service to its client. So the gas fee mainly on the computing resources (gas fee).
Tea project do both computing and state maintaining, so the tea project charges both commputing (gas) and memory tax(store data into the state machine). The state machine is a decentralized database. The memory size is a limited resources, so a Harberger tax model is used for those application store data inside. This is what Phala doesn't have.

## Tokenomics: Bonding curve
Tea project has a more complex tokenomics that using bonding curve ti incentivize the early stage miners, developers and investors. This is very useful when cold start. 

## Programming languages
Phala is compatible with Substrate smart contract. So Rust and Ink is used to write the fat contract.
Tea project is a web assembly runtime, it doesn't limit on what programming language the developer uses, as long as they can be compiled into wasm, it should run in Tea mini-runtime.

## SQL scripts supports
Phala provides computing services and the caller stores state in its blockchain. so it doesn't apply this.

Tea project has a decentralized state machine, besides the regular state update API, developers can use SQL scripts to query or update their applicaiton state "as if" there was a database running at the backend. SQL is the most common business applicaiton languages used by most developers.

## Front end hosting
Phala is a backend-as-a-service, it doesn't need to host front end. End user can run the front end from any hosting services, such as centralized AWS or decentralized IPFS.

Tea project is a three tiers architure, it has an end-to-end hosting solution. To make it fully decentralized, Tea applicaiton doesn't need DNS. It uses layer1 lookup an active IP to start the front end. So makes the application non-stoppable.
