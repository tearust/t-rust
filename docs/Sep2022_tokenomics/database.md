TApps has a state machine acts as traditional web application's database tier.

[state_maintainers](state_maintainers.md) are the nodes that runs the state machine and keep the state udpate and consensus.

Inside the state machine, there are a SQL instance and a TEA state memory object. TApps can use both SQL and TEA State OP_Code API to query or mutate state.
