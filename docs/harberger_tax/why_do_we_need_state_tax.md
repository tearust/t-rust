# What is state?

No matter what kind of blockchain project, all the efford of miners are to maintain a state. For example, the ledger of BTC is a state. All your ETH balanace is stored in the ETH blockchain. When end user want to change the state(eg. sending money to someone), they usualy send a signed transaction. Miners will execute the transaction and update the state. 

# What is state machine

State machine is the system that maintains the state. Miners are running the state machine. Miners' job is to run the state machine to maintain the state and make sure it is accurate and trustable.

# State is a limited public resource

In TEA Project, all the [Global_state_maintainer_nodes](Global_state_maintainer_nodes.md) are running the same state machine system and maintain a single state across all nodes. All TApps are sharing the same state. All state are stored in the memory of those nodes. All the state of every TApp are stored in the state. The size of the state is limited, if one TApp uses too much memory then other tapps won't have enough room to store their state. We will need a solution to keep every tapp share the usage of state efficiently.

# Memory tax is one solution to public resource

We use Memory Tax to increase the cost of overused tapp. Those tax money will be paid to the [Global_state_maintainer_nodes](Global_state_maintainer_nodes.md) since they spend their computing resource to maintain the global state. This tax is called Memory Tax. 

Memory Tax is billed based on how much memory a TApp uses and how long it occupy the space in the state. 

# Memory tax rate is calculated by the remaining free memory percentage

The Memory tax rate is not a fixed number. It is defined as a const value * (total memory size / remaining memory size). At the begining, the rate is the const value. if there is only half memory remaining (hald in use), the rate is double the const value. If there is only 25% memory free, the rate is 4 times of the const value. As the remaining memory less and less, the rate could become very high. The high rate will force some non-profit application either reduce the memory useage, or give up the competition.

# How to reduce Memory Tax?

To reduce the Memory Tax, either store less data in state or delete after use.

To reduce the size, one best way is to store blob data in the OrbitDB/IPFS. Every hosting CML has OrbitDB/PFS service in the hosting node. This is not a part of state machine. It is used as "cache" on the hosting node. This is much cheaper than the memory inside state machine. ONLY store really necessary data into the state machine.

As long as a state data is no longer needed, remove it as soon as possible to reduce the memory tax. Some data can be restored to OrbitDB/IPFS when they are no longer frequent access, or just delete to make room to other TApps.

# Global state maintainer is profitable, those head count become limited public resource too

Global maintainer can earn Memory tax. It is a profitable job. We cannot accept everyone to be a member of the [Global_state_maintainer_nodes](Global_state_maintainer_nodes.md), as the more maintainer, the lower efficient to reach consensus. Of course, there is a minimized requirement on the member of maintainers. As long as we reach a certain amount, there is no need to have more.

What if many miners want to become the [Global_state_maintainer_nodes](Global_state_maintainer_nodes.md)? How do we fairly select among them?

In many popular blockchain projects, they use Proof of Stake to choose the richest nodes to become maintainer. This is a very efficient solution, but it cause the rich gets richer problem. Over time, the monoply would be something inevitable.

# Harberger tax is the best solution

Harberger tax is the best solution for this kind of sceanario. Anyone can become a state maintainer as long as (1) Willing to pay a tax based on a self estimate price (2) Whoever pay higher than this self estimate price can take over the position to besome a maintainer.

This rule can use the invisible hands to adjust the price so that anyone can make decision to become a maintainer based his own financial decision. It is fair to everyone

# Where does the Harberger Tax money go?

In TEA Project, we call this tax "Maintianer income tax". Since this tax is taken from a public resources, it should be used for public usage and reward to public. 

## Public services

In TEA Project, we have public services(similar to Jury Duty in human society), which a miners node work for some tasks no one supposed to pay but for public seucurity purpose. Remote Attestation is one most common example. These kind of work is called public services. Maintainer income tax is used to pay for this service.

## Profit rewarding to stakers

If the Income tax cannot cover the public service, we will take fund from Genesis block miner reward reservation to cover. On the other hand, if there is remaining after pay all public service, this profit is paid to all [Global_bonding_curve](Global_bonding_curve.md) that all investors (stakers) who own the Global Token will share the dividend.

# Conclusion

Anyone can become a Global state maintainer by purchase a Global maintainer license from the Marketplace. In the first half year, you do not ever need to setup a mining machine since the TEA core team will maintain it on behalf of you. 

If you do not want to maintain a node, you can simply invest to the Global token buy in the [Global_bonding_curve](Global_bonding_curve.md). This is a standard Global Token follow the bonding curve rule.

As a developer, you will define your own business model. To reduce the cost, try to use as less as possible memory and store as short as possible. Besides the memory tax, other costs including Hosting service and Global txns fee.

Memory tax and Maintainer income tax are two important tokenomic rules in the TEA Project.  It helps improve the efficienty of the usage of public resource, also reward back to the community members.
