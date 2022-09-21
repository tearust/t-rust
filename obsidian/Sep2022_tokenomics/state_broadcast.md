TEA Project state constantly updates. Because there is no block concept in TEA project layer2. Every mutation transaction execution will update the state to a newer version.

The [[state_maintainers]] is always keep the latest / newest version of state in memory, but other nodes, such as [[hosting_nodes]] would have to subscribe and wait the new updates from [[state_maintainers]]. 

# State broadcast tiers
Because there are much more [[hosting_nodes]] than [[state_maintainers]], the state broadcast works like relay in tiers.

The top tier nodes will receive the latest state changes immediately. Then the first tier relay the changes to the second tier, then third tier, and so on.

The tiered relay can reduce the network workload for [[state_maintainers]]. The trade off is that the lower tier nodes will have significant delay  of state update. If a TApp is time sensitive, it either requie to run on a top tier [[hosting_nodes]] or use direct queries to [[state_maintainers]] which has additional costs.

