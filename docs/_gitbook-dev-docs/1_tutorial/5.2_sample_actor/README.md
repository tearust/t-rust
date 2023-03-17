Because most of new added logic stays in the new added sample-txn-executor, the sample-actor only add a new request handler. Those handlers are nothing but receive and relay to the state machine because all of them are supposed to be handled in the state machine SQL instances.

## dfn.rs

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

		_ => vec![],
	};
	Ok(res)
}
````

\``
You can find then handler is just a dispatcher, the logic will be in the api.rs.

## api.rs send requests

Let's use the delete_task as an example:

````
pub async fn delete_task(payload: Vec<u8>, from_actor: String) -> Result<Vec<u8>> {
	let req: DeleteTaskRequest = serde_json::from_slice(&payload)?;
  check_auth(&req.tapp_id_b64, &req.address, &req.auth_b64).await?;
	info!("Delete Task action...");

	let txn = Txns::DeleteTask {
    subject: req.subject.to_string(),
		auth_b64: req.auth_b64.to_string(),
	};

	request::send_custom_txn(
		&from_actor,
		"delete_task",
		&req.uuid,
		tea_sdk::serialize(&req)?,
		tea_sdk::serialize(&txn)?,
		vec![],
		TARGET_ACTOR,
	)
	.await?;

	help::result_ok()
}
````

We first created a DeleteTask txn, then use `send_custom_txn` utility to send it to state machine.

When using the send_customer_txn you will need to speicify yourself (the from_actor), txn name. The req.uuid is used for the client to query txn execution result at a later time. The TARGET_ACTOR is the name of receiver A actor, it is "someone.sample_txn_executor". If you are wondering where this name comes from, you can find it from the mainifest.yaml in sample-txn-executor/impl/manifest.yaml. This is how the TEA Project locate and identify every actor.

## check_auth

You may have noticed the `check_auth` in function `create_task`, but you did not see such a line in the function `query_task_list`. This is because anyone can query task list even without login, but one can only create a task with the creator to be one's own address. Of course, user no logged in cannot create task. So the check_auth is used to guard this.

Auth control is a large topic, in our tutorial we only explain how to use. For detail discussion please go to the developer document.

`  check_auth(&req.tapp_id_b64, &req.address, &req.auth_b64).await?`

This function will verify the req.auth_b64 matches the req.address from the recorded user log in data. If it failed, an error would be throw. Basicly, the auth_b64 is a session key. The TAppStore actor store a session information using this auth_b64 as key. The client need to keep the auth_b64 safely. For other malicious hacker, as long as they do not have this session key, it is hard for them to impersonate real user. Note, all the communication between browser and any TEA nodes are encrypted to prevent middle man attack and replay attack.

## faucet

Faucet is a special feature only live in the local dev-runner. The developer need some test token even during local testing. So we make faucet button and function to get 1000 free test token from DAO_RESERVE. Of course this will not be the case in the testnet or production.

Faucet request is handled by `"faucet" => api::txn_faucet(arg, from_actor).await?,`.

in api.rs:

````
pub async fn txn_faucet(payload: Vec<u8>, from_actor: String) -> Result<Vec<u8>> {
	let req: FaucetRequest = serde_json::from_slice(&payload)?;
  check_auth(&req.tapp_id_b64, &req.address, &req.auth_b64).await?;
	info!("Start faucet action...");

	let txn = TappstoreTxn::TransferTea {
    token_id: tappstore_id().await?,
    from: DAO_RESERVED_ACCOUNT,
		to: req.address.parse()?,
		amount: DOLLARS * 1000,
		auth_b64: req.auth_b64.to_string(),
	};

	request::send_tappstore_txn(
		&from_actor,
		"faucet_txn",
		&req.uuid,
		tea_sdk::serialize(&req)?,
		txn,
		vec![],
	)
	.await?;
	help::result_ok()
}
````

The main logic is wrapped into the `TappstoreTxn::TransferTea` function. 

TAppStore is the most important system actor that handles most system calls. You can get more from the developer document. In this step, we can see it requests transferring 1000 T from DAO_RESERVE to the current login user. The token_id is TAppStore not this TApp's id. This is very important, because all TEA token in the layer2 will be stored under the token_id == TAppStore. If the token_id == my_new_build_tapp, the currency is not TEA, but your own layer2  token (e.g. my_sample_task_coin). 
