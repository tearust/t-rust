Queries look up the state and returns the result of the state. 

Mutation changes the state and stores the value.

TEA Project's state is constantly changing. This is unlike the traditional blockchain that updates every few seconds/minutes. 

When queries return the result, it should always attach a TSID to indicate the version of the current state.

Most of the [[obsidian/Sep2022_tokenomics/hosting_nodes| hosting nodes]] have a cached state in memory. Most queries can be responsed directly from the hosting node without further querying to the [[obsidian/Sep2022_tokenomics/state_maintainers| state maintainers]], unless this query is time sensitive. Time sensitive means the user needs to get the most up to date data. This is beacuse the cached state in host nodes are slightly delayed due to the [[state_broadcast]] delay. Depending on if it's [[seated_hosting_nodes]] or unseated hosting, the delay may be up to a few seconds. 

