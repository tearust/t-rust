# Design principles

We encourage the hosting node to put more TEA deposit. So the higher TEA deposit, the higher chance for them to get high value task running. 

The TEA deposit is the CML total supply(total reserve in TEA of bonding curve). The miner doesn't have to create a bonding curve entity. The miner can pay all deposit on his own. That means a theta == 100, flat bonding curve that owned by the miner self.  No one else can invest.  Refer to [three_types_of_entity](three_types_of_entity.md)

# Algorithm

This only apply to hosting nodes, not state maintainer, since they do not run oracle tasks.

## Caclulate the weight for all hosting nodes

The weight of a hosting node is calculated based on the following factors

* The total deposit (total supply total reserve in TEA). For none profilt nodes, this value will be set to the max of all other profitable nodes.
* Accumulated credit. (this is a credit value, we do not have it yet)

## Sort all hosting nodes descend of weight

The largest weight hosting node stays the head of this queue.

## Each state maintainer nodes generate a random number. Cosnensus calcualte the min of all of them

We currently use coveyor to find the smallest random nonce from all the random number generated by every state maintainer node. 

## Map this smallest none to the array space sorted by hosting nodes' weight

If a node happens fall into this nonce in space, it is the winner

## Skip the busy nodes

If a hosting node is overloaded, skip it, try to get the one next to this busy node.(the right neighbor)

# Report system load from hosting nodes

The state maintainers need to know the hosting node's system load. Overloaded nodes should be except from running oracle tasks.

## New oracle task broadcast

When state maintainer receive new oracle task. The maintainer node who receive such task should ask every other A nodes to broadcast the notification to all connected hosting nodes. The notification is the basic requirement of this task. So that the hosting nodes who receive such notification will know if they are eligable to this oracle task or not. If they think they shoudl apply, they will go to the next step.

## Hosting node apply to task

The hosting nodes who want to execute oracle task should send apply message to any state maintainer node. This will result this application as a txn in the conveyor. A random nonce will be added at this monment. This random nonce will be used for select min nonce and locate the final winner.

## Notify the winner to execute

Whoever finally selected to be the winner will get notification. 

## Future remote attestation during oracle

This is a future feature that during the execution, the executor will be under remote attestation.