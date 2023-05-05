# Sample-actor Code Walkthrough - Login Branch

In the `login` branch, the majorify of the logic is in the frontend. There are only a few code changes in the backend to handle the login and faucet logic. Let's walk through it all.

We moved the function to handle "say-hello" from lib.rs to a new file called `dfn.rs`. A new handler "faucet" has also been added. This is used to send faucet [txn](../../../z_glossary/txn.md) when the user clicks the "faucet" button in the frontend.

````
use crate::error::Result;
use crate::api;
pub fn name_list() -> Vec<&'static str> {
	vec![
		"say-hello",
		"faucet",
	]
}

pub async fn map_handler(action: &str, arg: Vec<u8>, from_actor: String) -> Result<Vec<u8>> {
	let res = match action {
		"say-hello" => serde_json::to_vec("Hello world!").unwrap(),
		"faucet" => api::txn_faucet(arg, from_actor).await?,
		_ => vec![],
	};
	Ok(res)
}
````

When adding a new handler, make sure to also add the name to the `name_list`, because we'll need to let dfn::map_handler dispatch the request to the coresponding handler. 

In the future, almost all request handlers will be put here for easy organizing. 

Let's take a look at where these functions are called, for example in `lib.rs`:

````
impl Handle<HttpRequest> for Actor {
	async fn handle(&self, req: HttpRequest) -> Result<Vec<u8>> {
		let from_actor = String::from_utf8(NAME.to_vec())?;
		let base_res = map_handler(&req.action, req.clone().payload, from_actor.clone()).await?;
		let cur_res = crate::dfn::map_handler(&req.action, req.payload, from_actor).await?;
		if cur_res.is_empty() && !base_res.is_empty() {
			return Ok(base_res);
		}
		Ok(cur_res)
	}
}
````

This is the most important function. The `base_res` is used to handle **default** behaviors that can be handled by the `tea_sdk::utils`. In this case, it's the login request. 

So if the default handler is used and the `crate::dfn::map_handler` does not handle it (cur_res is empty), then the base_res is returned to the client. 

If you want to override the default handler, you can define the handler function inside of dfn.rs, so that the cur_res is no longer empty and it will be returned to the client. 

In this case, we didn't handle the login request, instead the default login handler inside the `tea_sdk::utils` is used. That's why we can write almost zero code to get the login feature.

In our TEA SDK, there are many default handlers like login. For more detail about the `tea_sdk::utils` please go to [tea_sdk_utils](tea_sdk_utils.md).

You may also notice that the AddRequest handler is removed given it's no longer used in this and future steps.

A new api.rs is added, and in this step it's mainly for the txn_faucet function. 

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

When the "faucet" request is received by the [actor](../../../z_glossary/actor.md), `crate::dfn::map_handler` will dispatch the function call to txn_faucet. Inside this function, we first generate the req:

````
let req: FaucetRequest = serde_json::from_slice(&payload)?;
check_auth(&req.tapp_id_b64, &req.address, &req.auth_b64).await?;
  
````

then use `TappstoreTxn::TransferTea` to convert it to a txn. `request::send_tappstore_txn` is used to send such a txn. This txn is sent to the [state machine](../../../z_glossary/state_machine.md) and executed async.

Note, we don't expect to get the execution result at this moment because all state machine nodes will handle async.

Please read the code carefully and understand each parameter. 
