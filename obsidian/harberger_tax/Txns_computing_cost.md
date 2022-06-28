![[Pasted image 20220618165714.png]]
Code inside state machine actors actually run inside the [[Global_state_maintainer_nodes]]. If there are 100 such nodes, all of the 100 nodes will run the same code to get the same state update. This is similar to ETH smart contract.

No doubt, it is more expensive than those Back end actor that runs inside a single hosting CML node.

The cost is calculated by the mini-wasm measured gas fee multiply the number of current active [[Global_state_maintainer_nodes]]. This fund is shared evenly to all [[Global_state_maintainer_nodes]]. 

