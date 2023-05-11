In this step, we will only focus on the changes related to the secure oracle. Other general changes follow the same logic we explained before, and will be ignored in this article.

In sample actor, the handler of complete_task will first send a `send_custom_txn` to the state machine with param `complete_task`. This tells the state machine to prepare a secured oracle task.

```
pub async fn complete_task(payload: Vec<u8>, from_actor: String) -> Result<Vec<u8>> {
	let req: CompleteTaskRequest = serde_json::from_slice(&payload)?;
  check_auth(&req.tapp_id_b64, &req.address, &req.auth_b64).await?;
	info!("Complete Task action...");

	let txn = Txns::CompleteTask {
    subject: req.subject.to_string(),
		auth_b64: req.auth_b64.to_string(),
	};

	request::send_custom_txn(
		&from_actor,
		"complete_task",
		&req.uuid,
		tea_sdk::serialize(&req)?,
		tea_sdk::serialize(&txn)?,
		vec![],
		TARGET_ACTOR,
	)
	.await?;

	help::result_ok()
}
```
In the future, the state machine should run a consensus to select a group of  hosting or worker nodes to execute this secured oracle task. But in our sample demo, we simplifly "callback" the current hosting node to execute this secure oracle task. This is a placeholder. 

After the state machine prepared for the execution of secure oracle. It will call back to this hosting node `complete_task_cb` . 

```
pub async fn complete_task_cb(payload: Vec<u8>, from_actor: String) -> Result<Vec<u8>> 


```
Inside this function, it trigger an API call `twitter_request` as below:
```{
	let req: CompleteTaskRequest = tea_sdk::deserialize(&payload)?;
	info!("Complete Task callback action...");

	let pass = oracle::twitter_request(&req.text, &req.subject).await;
```

This is a typical http API call but happened inside the enclave. If you want to know more about how this API call works, you can go to `src/oracle.rs`, the function `pub async fn twitter_request`. It looks nothing new but a typical twitter APi call, however it does't call regular HttpRequest as normal web2 backend. Let's see how it calls:

```
  let req = OracleHttpRequest {
    method: "GET".to_string(),
    url,
    headers: Some(headers),
    payload: None
  };
  let rs = ActorId::Static(tappstore_client::NAME).call(
    req,
  ).await?;
  let json: serde_json::Value = serde_json::from_str(&rs.text)?;
  
  ```

OracleHttpRequest is a struct param that used to trigger a system actor called `client-actor` to actually call the outside twitter server on behalf. Here is a copy of the OracleHttpRequest handler inside client_actor:
```
impl Handle<OracleHttpRequest> for ActorHandler {
	async fn handle(&self, req: OracleHttpRequest) -> Result<OracleHttpResponse> {
		let method: &'static str = Box::leak(req.method.into_boxed_str());
		let mut builder = request::http::Request::builder()
			.method(method)
			.uri(req.url)
			.body(match req.payload {
				Some(p) => p,
				None => "".to_string(),
			})?;

		if let Some(headers) = req.headers {
			for (key, val) in headers {
				builder.headers_mut().insert(
					request::http::HeaderName::from_str(&key)?,
					request::http::HeaderValue::from_str(&val)?,
				);
			}
		}

		let res = builder.request::<String>().await?;
		let text = res.into_body();
		Ok(OracleHttpResponse { text })
	}
}
```

For security reason, the user defined actor (such as sample-actor, sample-txn-actor) are not allowed to call outside http server directly. It can only use OracleHttpRequest and run through the whole oracle algorithm to have someone (state machine determine who) to call the request on behalf. This is how the security design comes in. 

Using the secure oracle algorithm, no developer, nor the end user can prediect which node or nodes (we call them executors) are desgnted to send/receive this http call. Even a few selected executors may be colluted or corupted, as long as the state machine consensus can follow the BFT rule, we can still get the truth as result. That is why we need to design the OracleHttpRequest and indirect delegation process. 

At the time of this tutorial is written, the executor selection and dispatching algorithm are still under development. You will not find out how they are expected to work at this moment. As a temporary placeholder, we just directly call back the calling hosting node as the executor. 

Now, let's see how we handle the http response. It is pretty much the same as the previous step. We only need to pass in the `pass:bool`  from the secure oracle. 

```
	let pass = if pass.is_err() {
		false
	} else {
		pass.unwrap()
	};

	let txn = Txns::VerifyTask {
    subject: req.subject.to_string(),
		failed: !pass.clone(),
		auth_b64: req.auth_b64.to_string(),
	};
	info!("Begin to send verify txn => {:?}", txn);
	request::send_custom_txn(
		&from_actor,
		"verify_task",
		&req.uuid,
		tea_sdk::serialize(&req)?,
		tea_sdk::serialize(&txn)?,
		vec![],
		TARGET_ACTOR,
	)
	.await?;

	if pass {
		help::result_ok()
	} else {
		help::result_error("Non-valid retweet. Please check that you've quote retweeted the source tweet and try the task again.".to_string())
	}

}
```

Note, this workflow may change after the secure oracle dispatching algorthrm is done. The call back function will become the executor function. It will be called by the state machine maintainer if this hosting node is seleccted to be an executor. No one (human) knows which node will be selected and no one knows what the final result will be, because the final result will be determined by the secure oracle consensus algorithm between the state machine maintainers. However, the basic steps would be the same as today as the following steps:
- Hosting node handle the reqeust from client
- Generate OracleHttpRequest
- Send theOracleHttpRequest to the state machine maintainers
- Callback (executor) function will be called if this hosting node is selected by the secure oracle consensus. Or nothing being called when not selected.
- Executor (if selected, or other node) execute the http request logic, send the result back to the  state machine
- State machine run the secure oracle consensus to determine the final result
- State machine send the final result back to the caller hosting node.
- Hosting node send back the final result to the client.