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

# Conclusion

Akash is kind of docker management platform. It leverage blockchain's token financial incentive feature. There is nothing special on the application.

TEA Project doesn't use docker due to seucrity reason. TEA has its own tokenomics, own database, own consensus, own execution environment. So it reach new level of decentralization and security.
