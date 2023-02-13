# Blockchain Listener

All [state machine replicas](State_Machine_Replica.md) nodes run a layer-1 client (either a full client or a light client). This layer-1 client will notify layer-2 when layer-1 events are triggered. The layer-2 runs a monitoring process that filters all events, only picking the events that are in the "waiting list" and then calling the layer-2 [state machine actor API](state_machine_actor.md). 

In the future, when the TEA Project is running above multiple layer-1 main chains, there would be multiple blockchain listeners for each main chain.
