Our cron job are set to trigger at certain block height. To do this, we have to frequently check the current blockheight by query the Ethereum API. This may cause too much dependency and cost of using such API. 

We can use human time instead, given we already have the GPS time signal to all nodes (B or A)

We use the human time to trigger cron job. Every A will trigger such cron job based on their own GPS time. When the time is up, every node will check their current block height and prepare all required data. Required data including anything need to get from the layer1 block, random seeds. Because different A node may get different block height. The consensus will be done inside the conveyor. Only one block height(and its block content) is finally selected and agreed by all others.
