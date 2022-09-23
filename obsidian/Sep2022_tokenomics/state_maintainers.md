State maintainers play a similar role as database servers in traditional cloud computing. They are a group of nodes running the TEA Project's state machine to keep a global state that's shared by all apps. 
They accept txns (transactions) from [[hosting_nodes]]. Those txns are queries (without changing the state) and mutations (change the state). 

Unlike traditional blockchains that updates the state in a new mined block every few seconds (or minutes), the state in TEA Project updates continuously like databases. 

## Maintainer's seats
There are limited number of the state maintainer in the TEA project. Each maintainer will be required to own a "Seat"to work. The seat can be obtained via an auction. The owner of the seat will need to pay a [[obsidian/epoch10_billing_tax/maintainer_tax| maintainer tax]].

##  State broadcasting
[[hosting_nodes]] can send query txns to state maintainers for the latest state, but in most cases, the hosting nodes will cache a historical state in its own node. They will receive the update of the latest state from state maintainers using state subscription model. The state maintainers only broadcast to a limited number of [[seated_hosting_nodes]] to reduce the network bandwidth consumption. 

The hosting nodes can become [[seated_hosting_nodes]] by obtaining the [[state_subscription_seat]] via an auction. This is similar to the state maintainer seat auction. Of course, they will need to pay tax too.