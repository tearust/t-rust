Hosting actor is the same functionality of back end in traditional web development. 
State actor is the same functionality of "stored procedure" development in traditional web development.

TEA's back end is a series handlers that handle requests from end user, process the business logic, then return the response back to the end user.

Typically there are three types of requests coming from the end users.

* Query 
* Send transaction
* Get results

## Send query

Query will not modify state, it just get the current state of state.

In some cases, the hosting nodes have local cache of state, so it will just return directly. This is a sync call. In other cases, the hosting nodes do not have local cache of state, so it cannot get back the result immediately. It has to send another request to the [obsidian/_gitbook-dev-docs/z_glossary/state_machine_actor](../_gitbook-dev-docs/z_glossary/state_machine_actor.md) for the latest state. In this case, the hosting node will response a UUID as query-stub for future result lookup. This call is considered an async call. The future looking up for result is "Get results" request. 

## Send transaction

Similar to query, but transaction (sometimes short for txns) will change the state. it will either modify the token balance or modify the SQL database.

Hosting nodes have no way to modify the state inside, because the state is owned by a group of state maintainer nodes. The hosting node instead can send a txn request to the state maintainer nodes to modify the state. No doubht, this is an async call. The hosting node will response an UUID as the query-stub for future result looking up.

## Get results

Once an async query or txns have been returned. The end user will receive an UUID as stub. The front end client will frequently send "Get Result" request to the connected Hosting node for the result of such UUID. Because there is no way to predict when the result will be ready, the front end may need to constantly ask for result if the answer is "not ready yet" until 1) the result is ready and returned, or 2) timed out, unlikely there would be an result in the future.

## Conclusion

Development of the hosting actor is nothing but to implmenet those three types of requests from front end. However, there are some "must-have" system level request handlers that all actor will need to handle. They are called [system_handler_for_actors](system_handler_for_actors.md). In most cases, developers do not need to modify the code and let it work by default. 
