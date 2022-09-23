TEA Project state constantly updates. Because there's no block concept in TEA project's layer2, every mutation transaction execution will update the state to a newer version.

The [state_maintainers](state_maintainers.md) are always keeping the latest / newest version of state in memory, but other nodes, such as [hosting_nodes](hosting_nodes.md) would have to subscribe and wait for the new updates from the [state_maintainers](state_maintainers.md). 
