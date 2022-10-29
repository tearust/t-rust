If an oracle service only query outside world data, it is query oracle. e.g. price feeder.
If an oracle service send out any mutation or action or anything that changes the state, such as sending a email, this is mutate oracle.

# Single mutate oracle

We will need state maintainer to select one hosting node to execute this oracle job.

The oracle job should have all random or preset value ready before put into the state and waiting for host nodes to pull and execute.

After execution,the host node will send another complete_oracle_job txn back to state maintainer. State machine will continue the call back smart contract workflow with the returned result.

# Multiple query oracle

We allow multiple nodes sending query request and make some average computing to get a more stable result.

# Workflow

* hosting node generate a txn send to state maintainers
* state maintainer use random seed to set a rule. any hosting nodes fits this rule can execute this oracle task. this oracle task stay in the state machine until complete
* Any hosting ndoes ping the state machine to check if eligable for an oracle task. If match, start to execute oracle
* Most oracles will have a callback logic. We can set the callback to be another actor txn call. That means when the outbound http response, the callback txn will be called and executed in the same hosting nodes to continue the remaining logic. Usually the remaining logic is parse the response, then send the compete oracle txn to state machine nodes
* The statemachine nodes receive the complete oracle txn, then do the remaining logic. In the multiple query oracle, there might be a waiting period to receive multiple hosting nodes to return their response, then make a average or filtering algorithm. this additional logic is an A node txn logic. Similar to multisig over threshold.
* When the oracle task complete, remove the temporary task in state. Return to caller if needed, or just send layer1 txn.

This workflow containers multiple p2p traffic, and all are async. it may take longer time to complete.
