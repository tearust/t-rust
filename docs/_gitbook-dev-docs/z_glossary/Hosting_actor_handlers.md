# Hosting Actor Handlers

The hosting actor has the same functionality as that of a back end in traditional web development.

The state actor has the same functionality as "stored procedures" in traditional web development.

TEA's back end is a series of handlers that handle requests from end-users, process the business logic, and then return the response back to the end user.

Typically there are three types of requests coming from the end users.

* Query.
* Send transaction.
* Get results.

## Send query

Query will not modify the state, it will just get the current state of the state.

In some cases, the hosting nodes have a local cache of state, so it will just return the result directly. This is a sync call. In other cases, the hosting nodes don't have a local cache of the state, so it cannot get back the result immediately. It has to send another request to the [state machine actore](state_machine_actor.md) for the latest state. In this case, the hosting node will response a UUID as query-stub for future result lookup. This call is considered an async call. The future looking up for result is "get results" request.

## Send transaction

Similar to query, but transaction (sometimes short for txns) will change the state. it will either modify the token balance or modify the SQL database.

Hosting nodes have no way to modify the state inside of their nodes because the state is owned by a group of state maintainer nodes. The hosting node instead can send a txn request to the state maintainer nodes to modify the state. No doubt, this is an async call. The hosting node will responsd with a UUID as the query-stub for a future look-up result.

## Get results

Once an async query or txns have been returned, the end-user will receive a UUID as stub. The front end client will frequently send "Get Result" request to the connected Hosting node for the result of such UUID. Because there is no way to predict when the result will be ready, the front-end may need to constantly ask for result if the answer is "not ready yet" until 1) the result is ready and returned, or 2) timed out as it would be unlikely there would be any result in the future.

## Conclusion

Development of the hosting actor is nothing but to implmenet those three types of requests from front end. However, there are some "must-have" system level request handlers that all actor will need to handle. They are called [system_handler_for_actors](../../dev_portal_design/system_handler_for_actors.md). In most cases, developers do not need to modify the code and let it work by default.
