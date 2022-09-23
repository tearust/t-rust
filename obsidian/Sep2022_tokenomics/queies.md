Queires look up state and returns the result at the state. 

Mutation changes the state stored value.

TEA Proejct state constantly change. This is unlike the traditioanl blockchain that updates every few seconds/minutes. 

When queires return the result, it should always attach a TSID indicate the version of current state.

Most of the [[obsidian/Sep2022_tokenomics/hosting_nodes| hosting nodes]] have its cached state in memory. Most queires can be responsed directly from the hosting node without further query to the [[obsidian/Sep2022_tokenomics/state_maintainers| state maintainers]], unless this query is time sensitive. Tiem sensitive means the user need to get the up most data. This is beacuse the cached state in host nodes are slightly delay due to [[state_broadcast]] delay. Depends on [[seated_hosting]] or unseated hosting, the delay may be up to few seconds. 

