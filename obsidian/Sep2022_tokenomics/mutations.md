TApp logic want to make change in the app state should use mutation transaction.

Mutation should be sent to [[obsidian/Sep2022_tokenomics/state_maintainers| state maintainer]] using short connection from a [[obsidian/Sep2022_tokenomics/hosting_nodes|hosting nodes]]. Note, the end user cannot request to [[obsidian/Sep2022_tokenomics/state_maintainers| state maintainers]] directly from his browser. Unless a directly connected end user browser client, A TEA box only talks to another trusted TEA box, not browser. 

Mutation was called command in previous documents.

Unlike traditional blockchain projecct, there is no block concept in TEA Project. The mutations do not need to be packed into a block then execute. It will be sorted using Conveyor algorithm and executed after a short delay. That is why the TEA Project state updates constantly.