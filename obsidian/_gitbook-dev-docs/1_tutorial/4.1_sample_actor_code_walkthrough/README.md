In the `login` branch, majorify of logic is in the front end. There are only few code changes in the backend. Let's walkthrough.

A new file called `dfn.rs` in creased. Inside we moved the function to handle "say-hello" from the lib.rs here.

```
use crate::error::Result;
pub fn name_list() -> Vec<&'static str> {
	vec![
		"say-hello",
	]
}

pub async fn map_handler(action: &str, _arg: Vec<u8>, _from_actor: String) -> Result<Vec<u8>> {
	let res = match action {
		"say-hello" => serde_json::to_vec("Hello world!").unwrap(),
		_ => vec![],
	};
	Ok(res)
}
```

In the future, almost all request handlers will be put here to easy organzing. 

Let's take a look at where these functions are called. Go to lib.rs

```
impl Handle<(), HttpRequest> for Actor {
	async fn handle(self, req: HttpRequest, _: ()) -> Result<Vec<u8>> {
		let from_actor = "sample_actor".to_string();
		let base_res = map_handler(&req.action, req.clone().payload, from_actor.clone()).await?;
		let cur_res = crate::dfn::map_handler(&req.action, req.payload, from_actor).await?;
		if cur_res.is_empty() && !base_res.is_empty() {
			return Ok(base_res);
		}
		Ok(cur_res)
	}
}
```

This is the most important function. The `base_res` is used to handle **default** behaviors that can be handled by the `tea_sdk::utils`. In this case, it is the login request. 

So if the default handler is used and the `crate::dfn::map_handler` does not handle (cur_res is empty), then the base_res is returned to the client. 

If you want to override the default handler, you can define the handler function inside the dfn.rs, so that the cur_res is no longer empty, and it will be returned to the client. 

In this case, we did not handle the login request, instead the default login handler inside the `tea_sdk::utils` is used. That is why we can write almost zero code to get login feature.

In our TEA SDK, there are many default handler like login. For more detail about the `tea_sdk::utils` please go to [[tea_sdk_utils]]

You may also noticed the AddRequest handler is removed given it is no longer used in this and future steps.

