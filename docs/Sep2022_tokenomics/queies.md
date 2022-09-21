Queires look up state and returns the result at the state. 

Mutation changes the state stored value.

TEA Proejct state constantly change. This is unlike the traditioanl blockchain that updates every few seconds/minutes. 

When queires return the result, it should always attach a TSID indicate the version of current state.

Most of the [ hosting nodes](hosting_nodes.md) have its cached state in memory. Most queires can be responsed directly from the hosting node without further query to the [ state maintainers](state_maintainers.md), unless this query is time sensitive. Tiem sensitive means the user need to get the up most data. This is beacuse the cached state in host nodes are slightly delay due to [state_broadcast](state_broadcast.md) delay. Depends on which broadcast tier seat a specific hosting node own, the delay may be up to few seconds. 
