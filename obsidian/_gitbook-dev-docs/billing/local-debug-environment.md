# Local Debugging Environment
TEA App developers can launch their own local debug environment through Docker. This local development environment has the following features:

- The debug environment comes as a self-contained Docker image that’s deployed to local machine.
- Single A node, single B node. Because these are only single nodes, there's no consensus for txns and no security. The developer also doesn't need to pay the memory tax or pay to use the hosting node.
- Full billing system support.
- Uses manifest to load actors.

These features of the local testing environment help developers estimate how much memory their app's actors will use as well as the gas cost. Because the manifest is used to load actors, there's no need to create a TApp first which saves the developer time.