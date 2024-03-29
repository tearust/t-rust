# Host actor

In our last step, we've completed updating the state_actor that handles all txns in the state machine. Those txns are originally sent from the hosting nodes, aka the back end  (or web servers) that handle client requests and then generate txns to the state machine. In this step, let's modify the actor that runs inside of the host nodes.

I'm going to call it host_actor to distinguish it from the state_actor, but note that the folder in our source code is named "actor". 

## No change in codec?

The difference between tutorial-v1 and idea-depot basically are their differing requests and responses owing to the different underlying data structures. That said, all requests and responses are still ultimately http requests and responses. If you take a look at the /actor/codec folder, you won't find anything related to requests and responses. That's because all basic http related code is embeded in the tea-sdk already. As a developer, you can use the tea-sdk functions by calling it in your code. For example, the following is from `/codec/impl/src/lib.rs`
:

````
use tea_sdk::utils::client_wasm_actor::types::{map_handler, HttpRequest};
......
......
impl Handles for Actor {
    type List = Handle![
        Activate,
        HttpRequest
    ];
}
````

Alright, so you know there's nothing you need to modify in the codec folder and can keep everything as it is. Let's focus on the `impl` folder only for the rest of this article.

## Update dfn.rs

The file `/actor/impl/src/dfn.rs` defines all the http requests this host actor will handle. The function `map_handler` is the entry point for all of them. So we'll need to update this file to remove old "tasks" related functions and add "idea" related functions in their place.

This is what the the `name_list` and `map_handler` functions look like BEFORE the changes:

````
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

````

This is the code AFTER the changes:

````
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
````

You can see that if the client sends an http request with a "name" in the "name_list", it will trigger an `api::name function` that's defined in the `map_handler`.

The next task will be to update the functions in `api.rs`.

## api.rs

We'll need to remove all "task" related functions add all "idea" related functions in the dfn.rs file we just updated. They're exactly what we designed in the previous system design step.

* create_idea
* vote_idea
* query_idea_list

All of these functions follow the same pattern: Parse incoming request, generate a txn, and then send the txn to the state machine. Of course some requests can be handled on the host node without needing to contact the state machine. These three requests happen to be the kind that interact with the state machine.

I will not paste all three functions because of their similarities and focus solely on `query_idea_list` as the example. The other functions follow the same workflow and logic.

````
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
````

Looking at the `query_idea_list` code first parses the incoming request `payload` and extract the `req.uuid`. Because there's no parameter we need to query, we'll end up querying all ideas in this case. Then we send a `request::send_custom_query` to the state machine. The `res` is an await result and will ultimately contain the response from the state machine. We generate the list of all ideas into a var called `x`. The last step is to save `x` to the `cache_json` data structure using `uuid` as the key.

You might wonder why we don't send the result back to the client directly and instead save to cache_json? Well, this is because all network traffic in TEA is async. After the client sends a query, the client isn't expected to get the result back immediately. It will send a `uuid` as a handle, telling the hosting node to save the result to this `uuid`. At a later time, the client will query the hosting node for the result using `uuid`. Once there's a result, the client will take the result back and stop further queries. Otherwise, keep querying until timeout is reached.

Thanks to the TEA core team, such cumbersome workflows are handled by the tea-sdk under the hood. You don't need to care how it works, just use it!

## types.rs

We have used `CreateIdeaRequest`, `VoteIdeaRequest` and `QueryIdeaRequest` but haven't defined them yet. Now we need to define them in `types.rs`:

````
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
````

## Build the actor

The rest of the source files are pretty much unchanged. They're just a typical TApp template. 

You can try to build the actor. If you get any compile errors, most likely you missed a step. You can use this guide as well as the earlier blog articles to troubleshoot and fix them on your own. 

Congratulation, you have reached the end of step 8. 
