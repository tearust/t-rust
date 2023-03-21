# Query logs

There is only one major change in sample-actor project.The sample-txn-executor has no change because it has nothing to do with gas fee logs. The sample-front-end added new UI but mainly just typical VUE web UI code change, no need to explain in our tutorial. So let's only focus on the sample-actor project.

We will need to add a new query: Get the list of logs. You can find this new function in the dfn.rs file
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
		"queryOpLogs",//this is the newly added function
	]
}
```

It is called "queryOpLogs" at the last.

In api.rs you can find the `async fn query_op_logs` function. The major logic is to call `get_statements_async` function. 
```
	get_statements_async(
		acct,
		date,
		IntelliSendMode::RemoteOnly,
		move |statements, read_to_end| {
			Box::pin(async move {
				info!(
					"read to end {}, get statements result: {:?}",
					read_to_end, statements
				);

				let mut rows: Vec<JsonStatement> = Vec::new();
				for item in statements {
					let s = item.0;
					let tmp = JsonStatement {
						account: format!("{:?}", s.statement.account),
						gross_amount: s.statement.gross_amount.to_string(),
						statement_type: s.statement.statement_type.to_string(),
						token_id: s.statement.token_id.to_hex(),
						state_type: s.state_type.to_string(),
						memo: item.2,
						time: item.1,
					};
					rows.push(tmp);
				}
				info!("log rows => {:?}", rows);
				let x = json!({ "logs": rows });
				help::cache_json_with_uuid(&uuid, x).await?;
				Ok(())
			})
		},
	)
```

In our runtime, we store log based on date. So we will query the backend which date to query.
```
	let date: Option<SimpleDate> = req
		.year
		.as_ref()
		.map(|year| SimpleDate::new(*year, req.month.unwrap_or(1_u32), req.day.unwrap_or(1_u32)));
```

Once we received the json response, the calback function format them to a UI friendly format, then `help::cache_json_with_uuid(&uuid, x).await?;` cache to local memory and waiting for the front end to check this result at a later time.

In the code above, you read a typical example on how to convert the data from the backend to the web UI friendly format. In your own TApp development, this is commonly used everywhere.



