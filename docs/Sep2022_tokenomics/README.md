# What does the TEA Project tokenomics do?

TEA Project provides a decentralized web3 apps hosting platform. Miners provide [special designed hardware](special%20designed%20hardware.md) called [hosting_nodes](hosting_nodes.md) to host application backend. End users can choose any agnostic hosting nodes to access web3 apps. A group of [state_maintainers](state_maintainers.md) together maintain a global state that shared by all apps. Our tokenomics are designed to provide a billing and taxing system to keep this ecosystem work smoothly.

# Players and their roles

## End users

Unlikely web2 world that most services are free to use to end users. In the blockchain or web3 world, most of the services are no longer free. The end users would have to pay for services. In the popular Ethereum terminology, it is called "gas fee". 

## Hosting node providers

They are sometimes called "miners" in the blockchain world. But in TEA Project, they do not waste hash power to maintain the global state. The specific [state_maintainers](state_maintainers.md) play that role instead. The miners here are simply hosting service providers. They own and run those [special designed hardware](special%20designed%20hardware.md) called "Tea Box". End user access to these tea boxes as if they are traditional internet servers. 

## State maintainers

[state_maintainers](state_maintainers.md) work as database servers in traditional cloud computing platform. They receive queries and mutations from [hosting_nodes](hosting_nodes.md), update state, broacasts state to [hosting_nodes](hosting_nodes.md) by tiers. 

## App owners

They build applications. In the TEA Project, we call them TApps(or tapps). TApps look similar to typical web application but runs decentrlized, so that they are

* Non stoppable
* Non breakable
* Censorship-free

## Investors (token holders or stakes)

Whoever owns CML tokens, Global tokens, TApp tokens are investors. They receive dividend as if they are traditional stock. Those tokens are different than stocks by the pricing model called [bonding_curve](bonding_curve.md).

# Basic data flow

Unlike traditional web apps that user need to go to a specific web URL to access a specific web app, in TEA Project, you can go to any [hosting_nodes](hosting_nodes.md)with the same application CID. The app runs the same regardless which [hosting_nodes](hosting_nodes.md) you access. This concept is similar to IPFS: You can go to any IPFS gateway with the same file hash called CID, you will get the same file regardless where it is feed from. TEA Project hosts dynamic content not static content as IPFS does.

Dynamic content means users can use apps to query and mutation of state (or database as usually called in web2). In traditional cloud computing, this layer who receive user interaction is called backend. In TEA project, they are called [hosting_nodes](hosting_nodes.md). In most cases, it is exactly the same [hosting_nodes](hosting_nodes.md) that you load your front end code from. Because the [hosting_nodes](hosting_nodes.md) has state cache, most queries can be done inside this node and returns to end users. For time sensitive queries or mutations, the [hosting_nodes](hosting_nodes.md) will send a txn to [state_maintainers](state_maintainers.md) to query latest state or update(mutate) state. 

[state_maintainers](state_maintainers.md) broadcasts latest state in tiers to [hosting_nodes](hosting_nodes.md), so that they can update their cached local state, in turn, they can provide up to date services to end user.

All nodes regardless [hosting_nodes](hosting_nodes.md) or [state_maintainers](state_maintainers.md) run computing logic inside enclave that protected by trusted computing hardware. So that they can be trusted and data secure.

# Basic money flow

## End users

End users are the ultimate source of all profit of the TEA eco system.

End users -> [hosting_nodes](hosting_nodes.md) gas fee for using the hosting services.
End users -> app_owners for in-app purchase (this how Apple calls it)

## Hosting nodes

### Services provided

* Feed static front end application code. There is no direct billing and profit from this service, but it is followed by the paid backend service from end users.
* Back end services. Queries and Mutations from end users. Typically what a traditional web servers do. Billing system will measure the computing resources consumped and end user pay in TEA token
* Keep a current global state and historical state data that will be used for queries
* Broadcast state changes to state subscribers (other [hosting_nodes](hosting_nodes.md))
* Public services, such as remote attestation

### Revenue source

* End user pay gas fee
* State subscription fee from the lower tier [hosting_nodes](hosting_nodes.md)

### Operating cost

* Purchase CML token
* Purchase tiered seat
* Paying tiered seat tax to Global token
* (Out of TEA eco system) Hardware and utility cost (network, electricity etc)
* Paying state subscription fee to up stream (upper level hosting nodes or state maintainer)

### Owned assets

* CML
* Tiered seat
* Initial investment to owned CML token (This is special front runner previllage to CML owner)

### Profit goes to CML token and shared by holders

## State maintainers

### Services provided

* Maintain the global state in secured memory
* Sort and execute txns quries and mutations to update the state
* Broadcast state changes to the top tier [hosting_nodes](hosting_nodes.md)

### Revenue source

* Applications pay [obsidian/harberger_tax/Txns_computing_fee](../harberger_tax/Txns_computing_fee.md)
* Applications pay [obsidian/epoch10_billing_tax/Memory_tax](../epoch10_billing_tax/Memory_tax.md)
* Top tier [hosting_nodes](hosting_nodes.md) pay state subscription fee

### Operating cost

* Purchase CML
* Purchase state machine [obsidian/epoch10_billing_tax/Maintainer_Seat](../epoch10_billing_tax/Maintainer_Seat.md)
* (Out of TEA eco system) Hardware and utility cost
* State maintainer income tax pays to the Global token

### Owned assets

* CML
* Seat

### Profit goes to maintainers themselves

## Application owners

### Services provided

* Application that end users actually use

### Revenue source

* In-App purchase

### Operating cost

* (Out of TEA ecosystem)Software development cost
* Pay [obsidian/harberger_tax/Txns_computing_fee](../harberger_tax/Txns_computing_fee.md) to state maintainer for both Queries and Mutations
* Pay [obsidian/epoch10_billing_tax/Memory_tax](../epoch10_billing_tax/Memory_tax.md) to state maintainers based on how much memory used in state
* NOTE: App owners DO NOT pay hosting fee to [hosting_nodes](hosting_nodes.md)

### Owned assets

Nothing in TEA ecosystem. Out of the TEA eco system, the IP, brand name etc.

### Profit goes to TApp token and shared by holders

### DAO members or Global token stakers

This is the DAO of TEA Project. All tea ecosystem members are supposed to be the Global token stake holder .

### Revenue sources

* State maintainer income tax
* Tiered hosting node seat tax
* Slashed assets from bad actors
* CML seeds auction

### Operating cost

* Public services: Remote attestation
* Future public affair (DAO management etc)

### Profit goes to Global token and shared by token holders
