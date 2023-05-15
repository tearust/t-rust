# Retweet Sample Actor
In this step we'll only focus on the changes related to the secure oracle. Other departures from the general Task TApp are largely superficial as explained before and won't be discussed in this article.

In the sample actor, the handler of `complete_task` will first send a `send_custom_txn` to the state machine with param `complete_task`. This tells the state machine to prepare a secure oracle task.

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
In the future, the state machine will run a consensus workflow to select a group of hosting or worker nodes to execute this secure oracle task. But in our sample demo, we simplifly "callback" the current hosting node to execute this secure oracle task. This will essentially be a placeholder until the full consensus algorithm is written. Note that consensus here is on the operational environment of the nodes to ensure hardware integrity, not on the result of the secure oracle process.  

After the state machine has prepared for the execution of the secure oracle, it will call back to this hosting node with `complete_task_cb` . 

```
pub async fn complete_task_cb(payload: Vec<u8>, from_actor: String) -> Result<Vec<u8>> 

```

Inside this function, it will trigger an API call `twitter_request` just as below:

```
	let req: CompleteTaskRequest = tea_sdk::deserialize(&payload)?;
	info!("Complete Task callback action...");

	let pass = oracle::twitter_request(&req.text, &req.subject).await;
```

This is a typical http API call but instead it happens inside the enclave. If you want to know more about how this API call works, you can go to `src/oracle.rs` and look at the function `pub async fn twitter_request`. It looks nothing new outside of what you'd see in a typical twitter APi call. However, it doesn't call a regular HttpRequest as a normal web2 backend would. Let's take a closer look on how it calls the twitter api:

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

For security reasons, the user defined actors (such as sample-actor and sample-txn-actor) are not allowed to call an outside http server directly. It can only use OracleHttpRequest and run through the whole oracle algorithm to have someone picked by the state machine to make the request on its behalf. This is how the security design of the TEA Project comes into play. 

Using the secure oracle algorithm, neither the developer or the end user can prediect which node or nodes (we call them executors) have been designated to send/receive this http call. Even if a few of the selected executors collude or are otherwise corupted, we can still get the true oracle result as long as the state machine consensus follows the BFT rule. This consensus on the integrity of the TEA node operational environments is a crucial part of the OracleHttpRequest and indirect delegation process. 

At the time of this tutorial is written, the executor selection and dispatching algorithms are still under development. We won't be able to elaborate on how they're expected to work at the present moment. As a temporary placeholder, we'll just directly call back the calling hosting node as the executor. 

Next up is to handle the http response which we'll tackle much the same as the previous step. We only need to pass in the `pass:bool` from the secure oracle.


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

Note that this workflow may change after the secure oracle dispatching algorithm is done. The call back function will become the executor function. It will be called by the state machine maintainer if a hosting node is selected to be an executor. No one can possibly know which node will be selected and no one knows what the final result will be. This is because the final result will be determined by the secure oracle consensus algorithm between the state machine maintainers. However, the basic steps would be the same as they are now and proceed through the following steps:

- The hosting node handles the request from the client.
- Next it generates OracleHttpRequest
- Then it sends theOracleHttpRequest to the state machine maintainers
- The callback (executor) function will be called if the hosting node is selected by the secure oracle consensus (else nothing is called when it hasn't been selected).
- The executor executes the http request logic, and sends the result back to the state machine. Note that the executor may or may not be the original hosting node. There also may be more than one executor depending on the requirements of the secure oracle.
- The state machine runs the secure oracle consensus to determine the final result. Given a variance of responses from the executors, the developer will need to write an algorithm to determine which is the "correct" result.
- The state machine sends the final result back to the calling hosting node.
- The hosting node sends the final result back to the client.