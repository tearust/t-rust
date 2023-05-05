# Query logs

In this `gas` branch, there's only one major change and it's in the **sample-actor** project. The **sample-txn-executor** has no change because it has nothing to do with gas fee logs. The sample-front-end added a new UI but mainly it's just typical VUE web UI code changes, so there's no need to explain it in our tutorial. So let's only focus on the **sample-actor** project.

We'll need to add a new query to get the list of logs and a txn to set allowance. You can find them in the `dfn.rs` file:

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
		"queryOpLogs",//this is the newly added function
		"setAllowance",
	]
}
````

It is called "queryOpLogs" and 'setAllownace' at the last.

//TODO:  !!!GEORGE, please add the explanation of allowance or link to our existing doc explaning Allowance!!!

In **api.rs** you can find the `async fn query_op_logs` function. The major logic is to call the `get_statements_async` function. 

````
	let (statements, read_to_end) = get_statements_async(
		acct,
		date,
		IntelliSendMode::RemoteOnly,
	)
	.await?;
````

then, convert  the rows to human readable format

````
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
````

In our runtime, we store records in the log based on date. So we'll query the backend on which dates to query.

````
	let date: Option<SimpleDate> = req
		.year
		.as_ref()
		.map(|year| SimpleDate::new(*year, req.month.unwrap_or(1_u32), req.day.unwrap_or(1_u32)));
````

Once we've received the json response, the calback function formats them to a UI-friendly format. After that, `help::cache_json_with_uuid(&uuid, x).await?;` caches it to local memory and waits for the front end to check this result at a later time.

The code above shows a typical example of how to convert the data from the backend to the web UI friendly format. In this tutorial we cover it for TApp development but it's the same formatting that's commonly used everywhere.
