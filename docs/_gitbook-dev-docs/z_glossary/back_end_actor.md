Using the TEA Party TApp as an example, the [party-actor](party-actor.md) compiles to the [actor](actor.md) that runs inside a [hosting_CML](hosting_CML.md). 

Since it's an [actor](actor.md), it's loaded and runs inside the [enclave](enclave.md) (also called the [mini-runtime](mini-runtime.md)).

The only thing that the back-end actor does is handle incoming requests.

This back_end_actor is different than the [state_machine_actor](state_machine_actor.md) which run inside the enclaves of the [state machine replica](State_Machine_Replica.md)s. Those [state machine actors](state_machine_actor.md) handle the [queries](queries.md) and [commands](commands.md) that directly interact with the [state machine](State_Machine.md). In traditional web 2.0 applications, these are usually called **Stored Procedures** that run inside the database server.
