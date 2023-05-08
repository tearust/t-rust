The **hosting actor** has the same functionality as the backend in traditional web development. 
The **state actor** has the same functionality of "stored procedure" development in traditional web development.

TEA's backend is a series of handlers that handles requests from the enduser, processes the business logic, and then returns the response back to the enduser.

Typically there are three types of requests coming from endusers:

* Query 
* Send transaction
* Get results

## Send query

A query will not modify the state. It will just get the current state of the state.

In some cases, the hosting nodes will have a local cache of the state so they'll just return the current state directly. This would be a sync call. In other cases, the hosting nodes don't have a local cache of the state, so it cannot return the result immediately. It will then have to send another request to the [state machine actor](../z_glossary/state_machine_actor.md) for the latest state. In this case, the hosting node will respond with a UUID as a query-stub for future lookups. This call is considered an async call, and the future result look up is called a "Get results" request. 

## Send transaction

Similar to queries, a transaction (txn) will change the state by either modifying the token balance or modifying the SQL database.

Hosting nodes have no way to modify the state inside, because the state is owned by a group of state maintainer nodes. The hosting node instead can send a txn request to the state maintainer nodes to modify the state. No doubht, this is an async call. The hosting node will response an UUID as the query-stub for future result looking up.

## Get results

Once an async query or txns have been returned. The end user will receive an UUID as stub. The front end client will frequently send "Get Result" request to the connected Hosting node for the result of such UUID. Because there's no way to predict when the result will be ready, the frontend may need to constantly ask for result if the answer is "not ready yet" until 1) the result is ready and returned or 2) time out, in which case it would be unlikely that there would be a result in the future.

## Conclusion

Development of the hosting actor is nothing but to implement those three types of requests from the frontend. However, there are some "must-have" system level request handlers that all actor will need to handle. They're called [system_handler_for_actors](system_handler_for_actors.md). In most cases, developers don't need to modify the code and can let it work by default. 
