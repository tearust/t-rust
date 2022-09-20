State maintainers play the similar role as database servers in traditional cloud computing platform. They are a group nodes runs the TEA Project state machine to keep a global state that shared by all apps. 
They accepts txns (transactions) from [hosting_nodes](hosting_nodes.md). Those txns are quries (without changing the state) and mutations(changing the state). 

Unlikely traditional blockchain that update state in a new mined block every few seconds(or minutes), the state in TEA Project updates continuously like databases. 

## Maintainer's seats

There are limited number of the state maintainer in the TEA project. Each maintianer will require to own a "Seat"to work. The seat can be obtain via an auction. The owner of seat will need to pay [obsidian/epoch10_billing_tax/maintainer_tax](../epoch10_billing_tax/maintainer_tax.md).

## State broadcasting

[hosting_nodes](hosting_nodes.md) can send query txns to state maintainers for latest state, but in most cases, the hosting nodes will cache a historical states in its own node. They will receive the update of latest state from state maintainer using state subscription model. This model runs in multi-tiers. The state maintainers only broadcasting to a limited number of "top tier" hosting nodes to reduce the network bandwidth consumption. Then the top tier hosting nodes broadcast to the "second tier", then "third tier" and so on.

The hosting nodes can join a tier by obtaining the tier seat via an auction. This similar to the state maintainer seat auction. Of course, they will need to pay tier tax too.
