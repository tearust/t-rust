Akash can be considered a tokenized Kubernates. Kubernates is a docker cluster management tool. When adding tokenized feature, Akash incentivize the miners to provider docker hosting service so that application can run on miner's machine instead of centralized Kubernates cluster.

Akash's tokenization is done by blockchain technology. But the docker services have nothing to do with blockchain. It is standard docker container. Existing cloud apps (dockernized) can run on Akash. **Those apps are NOT dApps, they are still existing cloud apps**.

This is very different than TEA Project. TEA Apps are not existing cloud apps. TEA Apps are designed to be decentralized apps.

# Common features
Both projects claim their applications are Nonstoppable.

# Differences
## Apps
Akash is a marketplace connecting cloud computing node provider and apps. It uses existing cloud computing docker technology. There is no special application for Akash app., All the apps are the same as existing apps run on cloud conputing. The selling point of Akash is the price. Akash claims the price is only 1/3 of AWS (reference https://akashlytics.com/price-compare)

TEA Applications are special execution format in WebAssembly. It is not Docker application, it will not run on AWS directly. Docker has no security consider the admin can login the system. Akash doesn't care this level of security because it is focus on cheap cloud computing (compare with AWS) of the same feature.

## Goal
Akash is still typical cloud computing. The goal is not to create a fully decentralized, censorship free apps

TEA Project is not trying to host existing cloud apps to miners' node. TEA Application is hardware level security guaranteed. Miner cannot enter the enclave, but Akash can.

## Container vs WebAssembly

Akash app is Container (docker) based. 

TEA Apps are WebAssembly based, and only run inside hardware enclave. 

The consequence is the miner can control the Akash docker server but can never login to the hardware enclave (TEA Project).

## Cloud or Blockchain?
Akash is a blokchain app. The goal of this app is to provide a marketplace for miners to host cloud computing apps. 

TEA Project is not blockchain app. It uses blockchain as one of the roots of trust. But TEA Apps are not running in blockchain. It runs on nodes (the same as Akash), but the node has no control over the app, because of hardware security.

## Consensus
Akash is a blockchain app. It uses blockchain consensus.

TEA Project has its own GPS timestamp-based consensus. It is actually not a consensus, but a hardware timestamp controlled distributed database. (The state maintainer).

## Comparable with AWS?
Akash is similar to AWS. The applications are run on both AWS or Akash. The advantage is cheaper price.

TEA Project is NOT AWS. TEA Application cannot run on AWS. So that TEA Apps have many new features that AWS cloud apps do not have. 

# Reasons to use
Apps need Akash could because
- Existing docker apps need cheaper host than AWS
- Concern of AWS to ban this application

Apps need TEA Project could because
- Top level privacy that concern of AWS admin or other Akash miners to get the secrets.
- Decentralized database support. 

# Reasons not to use
Apps may not use Akash because
- Not sure how Akash store globe state. I did not see a distributed database. So if any application transfer from node A to node B, not sure how the app state transfer to the new node.
- Worry about someone can login the node and get the secrets

Apps may not use TEA Project because
- Developer doesn't want to learn how to build TEA Apps
- Existing AWS docker apps cannot migrate without modification
- TEA is not ready yet

# Conclusion

Akash is kind of docker management platform. It leverage blockchain's token financial incentive feature. There is nothing special on the application.

TEA Project doesn't use docker due to seucrity reason. TEA has its own tokenomics, own database, own consensus, own execution environment. So it reach new level of decentralization and security.


# Reference
## Docker or Wasm?
![[Pasted image 20220708093126.png]]
This is from the commend of this video https://www.youtube.com/watch?v=UoRfr3Q2R8A

> A large benefit with wasm over containerization is the ability to be architecture agnostic. You can build and deploy the same wasm app which can then run on both ARM, x86, RISC without the developer choosing one ahead of time. This can give PaaS/FaaS companies extra flexibility to have different types of servers that run their users code without the user having to choose just one type. Wasm in this case with the component model also lets the platform expose functionality directly to the app versus through a roundabout api+sdk. Users can call into functions like a fetch() which simplifies your depedencies, not needing you to pull in reqwest+OpenSSL since the platform can just provide that for you. To me wasm is a step higher in the containerization chain, from containerizing your app to containerizing just your code. Just like how multiple containerized apps can use one kernel, multiple wasm apps can use the same platform code more directly. With the ~15% slowdowns I think a lot of this is still desirable to both developers and platforms alike. At the same time, I don’t expect this slowdown to be constant and as cranelift matures and wasm runtimes get more adoption we will see wasm’s overhead drop.

Why wasm runtime is a better choice for secure application than docker? The answer also apply to "why TEA project is a better choice for secure application than Akash"