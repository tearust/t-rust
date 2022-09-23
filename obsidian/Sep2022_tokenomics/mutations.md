Any TApp logic that wants to make changes in the app state should use mutation transactions.

Mutations should be sent to [[obsidian/Sep2022_tokenomics/state_maintainers| state maintainer]] using a short connection from [[obsidian/Sep2022_tokenomics/hosting_nodes|hosting nodes]]. Note the end user cannot request from the [[obsidian/Sep2022_tokenomics/state_maintainers| state maintainers]] directly from their browser. Unless directly connected to an end user browser client, A TEA box only talks to another trusted TEA box, not a browser. 

Mutation was called command in previous documents.

Unlike traditional blockchain projects, there's no block concept in TEA Project. The mutations don't need to be packed into a block then executed. It will be sorted using a Conveyor algorithm and executed after a short delay. That's why the TEA Project state updates constantly.