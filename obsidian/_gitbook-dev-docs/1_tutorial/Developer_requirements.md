# TEA Developer Prereqs

## Prerequisites for Becoming a TApp Developer

If you're an existing web developer in the cloud computing world we refer to as web2, then you're pretty much already a TApp developer. TApp development doesn't require any blockchain or distributed system skillsets. You can make your TApp as if it's a traditional 3-tier web app (front end, back end, and database).

## Basic programming skillsets

- Rust language (entry level).
- (Optional) SQL if you want to use SQL as your database.
- (Optional) Javascript if you also work on the front-end.

## Knowledge not required but nice to have

### Solidity / smart contracts

We don't require Solidity or smart contract knowledge although the TEA system itself is partially based on blockchain technology. Your business logic runs on the WebAssembly runtime inside enclaves that have nothing to do with smart contracts. However, if you're going to write any blockchain bridge that connects to any public blockchain, you will need to write smart contracts on that chain. This is out of the scope of the TEA Project.

### Distributed systems

TEA Project is a distributed system but it's tried its best to isolate the complexity under the hood. You, as a TApp developer, don't need to have any skill or knowledge about distributed systems. However, if you happen to have such knowledge, you can easily understand a lot of design choices made by the TEA Project. Those design choices may look weird from a traditional non-distributed way of thinking.

In most cases, you can design as if you're making a centralized traditional web2 application. The TEA Project has done all the heavy lifting work to make your code run decentralized across all nodes. Not only technically, but also financially. Financially meaning that even without knowning each other, there are other players in this ecosystem (miners, investors, maintainers) willing to work together to keep the system running because of the TEA Project's token incentives.