# Host actor
In our last step, we have done the state_actor change. We can handle all txns inside the state machine. Those txns are sent from the hosting node. The host nodes are those back end  (or called web servers) handle client request then generate txns to state machine. In this step, let's modify the actor that runs inside the host nodes.

I call it host_actor to distinguish from the state_actor. Actually the folder in our source code is called "actor". Just to make it clear. I do not want to change the folder name to "host_actor". 

## No change in codec?

The difference between tutorial-v1 and idea-depot are requests and responses. All of them are still http requests/responses. If you take a look at the /actor/codec folder, you will find nothing related to request and responses. That is because all basic http related code are embeded in the tea-sdk already. As a developer, just use it. The following line is from the /codec/impl/src/lib.rs

```
se tea_sdk::utils::client_wasm_actor::types::{map_handler, HttpRequest};
......
......
impl Handles for Actor {
    type List = Handle![
        Activate,
        HttpRequest
    ];
}
```

Alright, so you know there is nothing you need to modify in the codec. Keep it as is. Let's focus on the impl folder only in the rest of this article.

## Update dfn.rs

The file /actor/impl/src/dfn.rs defines all the http requests this host actor will handle. The function `map_handler` is the entry point of all of them. So we will need to update this file to remove old "tasks" related function, add "idea" related functions.

This is the name_list and map_handler function BEFORE the changes
```
pub fn name_list() -> Vec<&'static str> {
	vec![
		"say-hello",
		"faucet",
		"create_task",
		"query_task_list",
		"delete_task",
		"verify_task",
		"take_task",
		"complete_task",
		"init_db",
		"init_token",
		"setAllowance",
	]
}

pub async fn map_handler(action: &str, arg: Vec<u8>, from_actor: String) -> Result<Vec<u8>> {
	let res = match action {
		"say-hello" => serde_json::to_vec("Hello world!").unwrap(),
		"faucet" => api::txn_faucet(arg, from_actor).await?,
		"create_task" => api::create_task(arg, from_actor).await?,
		"query_task_list" => api::query_task_list(arg, from_actor).await?,
		"delete_task" => api::delete_task(arg, from_actor).await?,
		"verify_task" => api::verify_task(arg, from_actor).await?,
		"take_task" => api::take_task(arg, from_actor).await?,
		"complete_task" => api::complete_task(arg, from_actor).await?,
		"init_db" => api::init_db(arg, from_actor).await?,
		"init_token" => api::init_token(arg, from_actor).await?,
		"setAllowance" => api::set_allowance(arg, from_actor).await?,

		_ => vec![],
	};
	Ok(res)
}

```
This is the code AFTER the changes
```
pub fn name_list() -> Vec<&'static str> {
	vec![
		"ping",
		"faucet",
		"create_idea",
		"query_idea_list",
		"vote_idea",
		"init_db",
		"init_token",
		"queryOpLogs",
		"setAllowance",
	]
}

pub async fn map_handler(action: &str, arg: Vec<u8>, from_actor: String) -> Result<Vec<u8>> {
	let res = match action {
		"ping" => serde_json::to_vec("pong").unwrap(),
		"faucet" => api::txn_faucet(arg, from_actor).await?,
		"create_idea" => api::create_idea(arg, from_actor).await?,
		"query_idea_list" => api::query_idea_list(arg, from_actor).await?,
		"vote_idea" => api::vote_idea(arg, from_actor).await?,
		"init_db" => api::init_db(arg, from_actor).await?,
		"init_token" => api::init_token(arg, from_actor).await?,
		"queryOpLogs" => api::query_op_logs(arg, from_actor).await?,
		"setAllowance" => api::set_allowance(arg, from_actor).await?,

		_ => vec![],
	};
	Ok(res)
```

You can see if the client send a http request with a "name" in the "name_list", it will trigger a api::name function defined in the map_handler.

Right now, the api.rs has not been updated yet, let's move on.

## api.rs

We will need to remove all "task" related function, add all "idea" related function defined in the dfn.rs we updated just now. They are exactly what we designed in the previous system design step.

- create_idea
- vote_idea
- query_idea_list

All of these functions follow the same pattern: Parse incoming request, generate a txn, send the txn to the state machine. Of course, some request can be handled on the host node without contacting the state machine. These three requests happen to be the rest of them.

I will not paste all three functions, let's use query_idea_list as one example:
```
pub async fn query_idea_list(payload: Vec<u8>, from_actor: String) -> Result<Vec<u8>> {
	let req: QueryIdeaRequest = serde_json::from_slice(&payload)?;
	info!("Start query_idea_list...");

	let uuid: String = req.uuid.to_string();

	let res = request::send_custom_query(
		&from_actor,
		IdeaQueryRequest {
			owner: None,
		},
		TARGET_ACTOR,
	)
	.await?;

	let r: Vec<Idea>  = res.0;
	let x = serde_json::json!({
		"list": r,
	});
	info!("query_idea_list => {:?}", x);
	help::cache_json_with_uuid(&uuid, x).await?;

	help::result_ok()
}
```

First, parse the incoming request `payload`. We only need the `req.uuid`. Because there is no parameter we need to query, we are query all ideas in this case. Then `request::send_custom_query` to the state machine. The `res` is a await result. It contains the response from the state machine. We generate the list of all ideas into a var called `x`. The last step is to save the `x` to the cache_json data structure using uuid as the key.

What? Why do not we send the result back to the client directly? Why do we save to a cache_json? Well, this is beause all network traffic in TEA is async. After the client send query, the client is not expected to get the result immediately. It will send a `uuid` as a handle, telling the hosting node to save the result to this uuid. At a later time, the client will query the hosting node for the result using uuid. Once there is a result, the client will take the result back and stop further query. Otherwise, keep query until timeout.

Thank the TEA core team, such curbersome workflows are handled by the tea-sdk under the hood. You do not need to care, just use it!

The other functions follow the same workflow and logic. Please write the code on your own.

## types.rs

We have used CreateIdeaRequest, VoteIdeaRequest and QueryIdeaRequest but have not defined them yet. Now we need to define them in types.rs
```
#[derive(Debug, Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct CreateIdeaRequest {
  pub uuid: String,
  pub address: String,
  pub tapp_id_b64: String,
  pub auth_b64: String,

  pub id: String,
  pub title: String,
  pub description: String,
  pub unit: String,
}

#[derive(Debug, Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct VoteIdeaRequest {
  pub uuid: String,
  pub address: String,
  pub tapp_id_b64: String,
  pub auth_b64: String,

  pub id: String,
  pub price: String,
}


#[derive(Debug, Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct QueryIdeaRequest {
  pub uuid: String,
}
```

## Build the actor

The rest of the source files are pretty much unchanged. They are just a typical TApp template. 

You can try to build the actor. If you get any compiling error, most likely missing pieces. You can fix them on your own. 

Congratulation, you have reached the end of step 8. 

