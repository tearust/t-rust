# What does the TEA Project tokenomics do?

TEA Project provides a decentralized web3 apps hosting platform. Miners provide [special designed hardware](special%20designed%20hardware.md) called [hosting_nodes](hosting_nodes.md) to host the application [backend](backend.md). End users can choose any agnostic hosting nodes to access web3 apps. A group of [state_maintainers](state_maintainers.md) together maintain a global state that's shared by all apps. Our tokenomics are designed to provide a billing and taxation system to keep this ecosystem working smoothly.

# TEA Ecosystem Players and their roles

## End users

Unlike the web2 world where most services are free to use for end users, the blockchain or web3 world's services are generally no longer free. The end users has to pay for services. In the popular Ethereum terminology, this is called a "gas fee". 

## Hosting node providers

Hosting node providers are sometimes called "miners" in the blockchain world. But in TEA Project, they don't waste hash power to maintain the global state (specific [state_maintainers](state_maintainers.md) play that role instead). The miners in the TEA Project are simply hosting service providers. They own and run [special designed hardware](special%20designed%20hardware.md) called "Tea Boxes". End users access these tea boxes as if they were traditional internet servers. 

## State maintainers

[state_maintainers](state_maintainers.md) work just like database servers in the traditional world of cloud computing. They receive queries and mutations from [hosting_nodes](hosting_nodes.md), update the state, and broacast the current state to [seated_hosting](seated_hosting.md).

## App owners

App owners build applications which in the TEA Project are called TApps. TApps look similar to typical web applications but run decentrlized, which makes them

* Non stoppable
* Non breakable
* Censorship-free

## Investors (token holders or stakes)

Whoever owns CML tokens, Global tokens, or TApp tokens are investors. They receive dividends as if they held traditional dividend paying stocks. But these tokens are different than stocks by the virtue of the pricing model the follow called a [bonding_curve](bonding_curve.md).

# Basic data flow

Unlike traditional web apps where users need to go to a specific web URL to access a specific web app, in the TEA Project, you can go to any of the [hosting_nodes](hosting_nodes.md) using the same application CID. The app runs the same regardless which [hosting_nodes](hosting_nodes.md) you select. This concept is similar to IPFS: you can go to any IPFS gateway with the same file hash called a CID, and you will get the same file regardless where it's fed from. But the TEA Project hosts dynamic content and not static content as IPFS does.

Dynamic content means users can use apps to query and mutate the state (or update the database as it's usually called in web2). In traditional cloud computing, the layer that receives user interaction is called the backend. In the TEA project, they are called [hosting_nodes](hosting_nodes.md). In most cases, it will be exactly the same [hosting_nodes](hosting_nodes.md) that you load your front end code from. Because the [hosting_nodes](hosting_nodes.md) have a state cache, most queries can be done inside of that node and return the result directly to end users. For time sensitive queries or mutations, the [hosting_nodes](hosting_nodes.md) will send a txn to [state_maintainers](state_maintainers.md) to query the latest state or update (mutate) the global state. 

[state_maintainers](state_maintainers.md) broadcast the latest state to [seated_hosting](seated_hosting.md), so that they can update their cached local state. This allows them to provide up to date services to end users. Unseated hostings can get the state updates from the [seated_hosting](seated_hosting.md). 

All nodes regardless if they're [hosting_nodes](hosting_nodes.md) or [state_maintainers](state_maintainers.md) run computing logic inside of enclaves that are protected by trusted computing hardware. This is so that their computational results can be trusted and the data has a space to remain secure.

# Basic money flow

## End users

End users are the ultimate source of all profit of the TEA ecosystem.

End users -> [hosting_nodes](hosting_nodes.md) gas fee for using the hosting services.

End users -> app_owners for in-app purchase (this is how it's called in platforms like Apple's).

## Hosting nodes

### Services provided

* Feed static front end application code to end user. There's no direct billing and profit from providing this service, but it's followed up by the same node providing paid backend services to the end user.
* Provide back end services including queries and mutations to end users. Typically what traditional web servers do. TEA's billing system will measure the computing resources used and the end user pays in TEA token.
* Keep current global state and historical state data that will be used for queries.
* Broadcast state changes to state subscribers (other [hosting_nodes](hosting_nodes.md)).
* Public services, such as remote attestation.

### Revenue source

* End user pay gas fee.

### Operating cost

* Purchase CML token.
* Purchase [state_subscription_seat](state_subscription_seat.md).
* Pay [state_subscription_seat](state_subscription_seat.md) tax to Global token.
* (Outside of TEA ecosystem) Hardware and utility costs (network, electricity etc).
* Paying state subscription fee to up stream (upper level hosting nodes or state maintainers).

### Owned assets

* CML
* [state_subscription_seat](state_subscription_seat.md)
* Initial investment of own CML token (this is special front runner privilege to the CML owner).

### Profit goes to CML token and is shared by holders

## State maintainers

### Services provided

* Maintain the global state in secure memory.
* Sort and execute txns quries and mutations to update the state.
* Broadcast state changes to the [seated_hosting](seated_hosting.md)

### Revenue sources

* Applications pay [transaction execution fee](../harberger_tax/Txns_computing_fee.md).
* Applications pay [ memory tax](../epoch10_billing_tax/Memory_tax.md).
* [seated_hosting](seated_hosting.md) pay state subscription fee.

### Operating costs

* Purchasing CML.
* Purchasing state machine [ maintainer seat](../epoch10_billing_tax/Maintainer_Seat.md).
* (Outside of the TEA ecosystem) Hardware and utility costs.
* State maintainer income tax paid to the Global token.

### Owned assets

* CML
* Seat license

### Profit goes to maintainers themselves

## Application owners

### Services provided

* Application that end users actually use.

### Revenue source

* In-App purchases.

### Operating cost

* (Outside of TEA ecosystem) Software development costs.
* Pay the [ transacction execution fee](../harberger_tax/Txns_computing_fee.md) to state maintainers for both Queries and Mutations.
* Pay the [ memory tax](../epoch10_billing_tax/Memory_tax.md) to state maintainers based on how much state memory they use.
* NOTE: App owners DO NOT pay any hosting fees to [hosting_nodes](hosting_nodes.md).

### Owned assets

Nothing in TEA ecosystem. Out of the TEA ecosystem, the IP, brand name etc.

### Profit goes to the TApp token and is shared by holders

### DAO members or Global token stakers

This is the DAO of TEA Project. All tea ecosystem members are encouraged to become Global token stake holders.

### Revenue sources

* State maintainer income tax.
* [state_subscription_seat](state_subscription_seat.md).
* Slashed assets from bad actors.
* CML seed auctions.

### Operating cost

* Public services: Remote attestation
* Future public affair (DAO management etc.)

### Profit goes to Global token and is shared by token holders
