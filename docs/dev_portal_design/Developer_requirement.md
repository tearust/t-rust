## Who can become a TApp develope

If you are an existing web developer (we usually call Web2), you are pretty much already a TApp developer. TApp development doesn't require any blockchain or distributed system skillsets. You can make your TApp as if it is a traditional  3-tiers web app (front end, back end, database). 

## Basic programming skillsets

* Rust language entry level
* Optional SQL if you want to use SQL as your database
* Optional Javascripts if you also work on front end. 

## Knowledge not required but nice to have

### Solidity or Smart contract

We do not need Solidity or smart contract skill although the TEA system itself  is partially based on blockchain technology. Your business logic runs on the WebAssembly runtime inside enclave that has nothign to do with smart contract. However, if you are going to write any blockchain bridge that connect to any public blockchain, you will need to write smart contract on that chain. This is out of the scope of the TEA Project.

### Distributed system

TEA project is a distributed system but it tried its best to isolate the complexity under the hood. You, as a TApp developer, do not need to have the skill or knolwedge about distributed system. However, if you happen to have such knowledge, you can easily understand a lot of design choice of TEA Project. Those design choices may look weird from a traditional non-distributed way-of-thinking.

In most cases, you can assign that you are making a centralized traditional web 2 application. The TEA Project has done all the heavy lifting work to make your code run decentralized across all nodes. Not only technically, but also financially. Financially means without knowning each other, there are other players in this ecosystem (miners, investors, maintainers) willing to work together to keep the system running under token incentivize. 
