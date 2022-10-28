
If an oracle service only query outside world data, it is query oracle. e.g. price feeder.
If an oracle service send out any mutation or action or anything that changes the state, such as sending a email, this is mutate oracle.

# Single mutate oracle

We will need state maintainer to select one hosting node to execute this oracle job.

The oracle job should have all random or preset value ready before put into the state and waiting for host nodes to pull and execute.

After execution,the host node will send another complete_oracle_job txn back to state maintainer. State machine will continue the call back smart contract workflow with the returned result.

# Multiple query oracle

We allow multiple nodes sending query request and make some average computing to get a more stable result.

