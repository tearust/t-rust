# Troubleshooting techniques

No matter how carefully you read and follow instructions, it shouldn't be surprising that you get many error messages instead of a running app. This is basically life as a developer. Now let's review some of the problems I experienced during my development of this example TApp. It's impossible to list all the potential troubles you may experience. These troubleshooting tips will help you to fix your own issues. If you're still stuck somewhere, go to our telegram channel and ask us as well as other community members. We're happy to help you figure it out together!

## Rust compiling error

Even just a simple package name replacement can cause tens or even hundreds of error messages. Don't panic, this is very common and one of the easiest problems you can fix.

These types of error messages are commonly complain missing files or missing package. It's mostly caused by changing the package name in one place, but the original code in the other places are still looking for the original file or package. Please read the error message carefully, and findout the places you forgot to replace. I can also share a small trick. To minimize your work load and make the troubleshooting scope managable, always, always make only one or a few changes between each compile. If there are any error message during this compile, it's likely related the changes you just made. In this case, the scope is much smaller and more manageable for you to find out the solution. After it compiles ok, move on to the next change.

Although the Rust compiler is very picky, it's one of the most user friendly compilers. The error messages it gives are usually very helpful. Of course you'll need some Rust or general programming experience to handle them.

## Node version and NPM

Node.js is used in our front end development and local testing. Node.js is well known by its confusing version management. There is a chance that your local node.js version works with some apps but not with your current TApp. At the time of writing, I'm using node.js version 14.14.0 as laid out in the TEA dev docs. If you get any problem potentially related to the node verison, please use `nvm` and switch it back to 14.14.0. Hopefully it works! If you have any issues regarding your npm version, note that the version I used successfully is 6.14.8.

## Docker version

Our community member Yong has reported that his docker version 19.x won't work with our dev-runner. So please update your docker version to 20+. We really appreciate and encourage any reports we get from our community.

## Wrong token_id

If you didn't replace your token_id when building a new tapp, or you set your token_id in one place, but didn't change where it's referenced (e.g. you did not update the front end `.env.test` file), you may get a very tricky error message. The error may look like the following:

* complain that the actor isn't loaded.
* complain that the request hasn't been handled.
* An io error.
* An error or warning during deployment in the Developer Portal.

These error messages are most likely runtime errors, so it may be harder to find, and it takes a longer response loop because there's no errors during compiling. But when each tier (front end, back end, state machine) communicate with others, the token_id would be mismatched. 

If you find related errors, the first suspicion should be the token_id. Make sure you've updated the token_id in all tiers including the front end(`.env.test ,  .env.prod`), the actor (backend) and the state actor (state machine). Also make sure you set it to the same token_id that you obtained from "create tapp" in the Developer Portal. It is ok to set to any token_id during testing, but make sure you do not use the same number that also used in other actors that still in used by the dev-runner. For example, let's say you have the tutorial-v1 actor in the dev-runner local/b-node folder (the sample-actor.wasm), and you compiled the new idea-depot actor in the same folder (named actor.wasm). If you forgot to replace token_id to 0x111111, your new actor.wasm will have the same token_id as the old tutorial-v1 sample-actor.wasm which is 0x00000. This will cause problems when the dev-runner loads both wasm actors. 

## How do I know the actors are loaded correctly?

All functions you write in the code can only take effect after the actors are loaded into dev-runner. If any reason cause the actor load fail, no doubt your app won't work. 

There is a system request called `Activate` that's called after an actor's loaded. So if the actor receives this request, it will write a log so that you can find this log to determine if the actor is successfully loaded.

The Activate handler is in every actor. For example, in actor/impl/src/lib.rs:

````
    async fn handle(&self, _: Activate) -> Result<()> {
        let list = [&map_fn_list()[..], &crate::dfn::name_list()[..]].concat();
        register_adapter_http_dispatcher(list.iter().map(|v| v.to_string()).collect()).await?;
        info!("activate idea vote actor successfully");
        Ok(())
    }
````

So if you can see the log "active idea vote actor successfully" in the docker log, you'll know at least that your actor is loaded and responding to the Activate request.

If you cannot find such a log, you would go to check if the actor.wasm is correctly compiled and copied to the dev-runner/local/b-node (or a-node for state actor) and also check if the wasm file is signed correctly using the key.pem. 

If you start dev-runner using  `docker-compose up`, all docker containers will send output to the same terminal. Sometimes it's hard to search. You can use `docker-compose up -d` to start docker as a daemon. In this case, you'll need to learn some docker commands to read the logs. You can go to [docker.com](https://docker.com) to learn more. 

## Initialization

If you test everything locally using dev-runner, you'll need to manually intiialize the token and database from the admin page. When deploying your app to testnet or mainnet, you'll do so in the deployment step in the Developer Portal. In your local test, you'll need to manually click the two buttons in the admin page. If you forget to do so, you'll get an error message in almost every click (e.g. login, or faucet).

## What? No_wallet?

If you see this "no_wallet" on the UI, you haven't setup the Metamask browser extension correctly. 

Please install the Metamask browser extension then refresh your web page to fix.

## Forgot to add some funds or set spending limit

If you test locally using dev-runner, you'll need to get some free test tokens from the faucet button in the account page. This is built-in feature to simplify your testing experience. In the real world, there's no such faucet. You'll need to somehow get some tokens and top up to your account before you can use your own app. 

If you don't have enough balance but run some task that costs some TEA, you'll get error message such as `.... cross-move failed....from .... to .... balance ... overdraft` or something similar. When you read this message, you should know you'll need more tokens in your balance.

Besides some tokens in your account balance, you'll also need to allow this app to use a certain amount of tokens, which is called setting the spending limit. Failing to do so will also cause similar error messages, because the app will be rejected when trying to run some transactions that costs fund but it doesn't have enough of a spending limit.

## Still struggling? Do not give up, take this shortcut

If you're still struggle with all the troubles after trying all you can do, please don't give up. Let's take a shortcut to the final stage. 

You can go to https://github.com/tearust/idea_depot/tree/step6 and clone code from there and checkout directly from step6. That will show you what the code should look like at the end of our step5. 
