TEA Project state constantly updates. Because there is no block concept in TEA project layer2. Every mutation transaction execution will update the state to a newer version.

The [state_maintainers](state_maintainers.md) is always keep the latest / newest version of state in memory, but other nodes, such as [hosting_nodes](hosting_nodes.md) would have to subscribe and wait the new updates from [state_maintainers](state_maintainers.md). 

# State broadcast tiers

Because there are much more [hosting_nodes](hosting_nodes.md) than [state_maintainers](state_maintainers.md), the state broadcast works like relay in tiers.

The top tier nodes will receive the latest state changes immediately. Then the first tier relay the changes to the second tier, then third tier, and so on.

The tiered relay can reduce the network workload for [state_maintainers](state_maintainers.md). The trade off is that the lower tier nodes will have significant delay  of state update. If a TApp is time sensitive, it either requie to run on a top tier [hosting_nodes](hosting_nodes.md) or use direct queries to [state_maintainers](state_maintainers.md) which has additional costs.
