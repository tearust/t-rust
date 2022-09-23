TEA Project state constantly updates. Because there is no block concept in TEA project layer2. Every mutation transaction execution will update the state to a newer version.

The [state_maintainers](state_maintainers.md) is always keep the latest / newest version of state in memory, but other nodes, such as [hosting_nodes](hosting_nodes.md) would have to subscribe and wait the new updates from [state_maintainers](state_maintainers.md). 
