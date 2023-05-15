# Troubleshooting techniques

No matter how carefully you read and follow instructionx, it won't be surprising that you get many error messages instead of a running app. This is basically life as a developer. Now let's review some of the problems I experienced during my development of this example TApp. It's impossible to list all the potential troubles you may experience. These troubleshooting tips will help you to fix your own issues. If you're still stuck somewhere, go to our telegram channel and ask us as well as other community members. We're happy to help you figure out together!

## Rust compiling error

Even just simple package name replacement may cause tens even hundreds of error messages. Do not be panic, this is very common and the easiest problem you can fix.

These type of error messages are commonly complain missing files or missing package. It mostly caused by your change the package name in one place, but the original code in other places are still looking for the original file or package. Please read the error message carefully, and findout the places you forget to replace. Here let me tell you a small trick. To minimize your work load and make the troubleshooting scope managable, always, always make only one or a few changes between each compile. If there is any error message during this compile, likely related the changes you just made. In this case, the scope is much smaller for you to find out the solution. After it compiled ok, move on to the next change.

Although the Rust compile is very picky, it is one of the best user friendly compiler. The error message is usually very helpful. Of course you will need some Rust or general programming experience to handle them.

## Node version and NPM

Node.js is used in our front end development and local testing. Node.js is well known by its confusing version management. There is a chance that your local node.js version works with some apps but not with your current tapp. At the time of writing, I am using node.js version 14.14.0 and it works just fine. If you get any problem potentially related to node verison, please using `nvm` to switch it back to 14.14.0. Hopefully it works! So does for npm version, mine is 6.14.8.

## Docker version

Our community member Yong has reported that his docker version 19.x won't work with our dev-runner. So please update your docker version to 20+. Please report similar problem like what Yong did. Thank you.

## Wrong token_id

If you did not replace your token_id when build new tapp, or you set your token_id in one place, but did not change where it is referenced (e.g. you did not update the front end `.env.test` file), you may get very tricky error message. The error may look like the following:

* complain actor not loaded
* complain request not handled
* io error
* Error or warning during deployment in Developer Portal

These error messages are most likely runtime error, so it may be harder to find, and it takes longer response loop. Because there is nothing wrong during compiling. But when each tier (front end, back end, state machine) communicate with others, the token_id would be mismatched. 

If you find related error, first impression should be the token_id. Make sure you update the token_id in all front end(`.env.test ,  .env.prod`), actor (backend) and state actor(state machine). Also make sure you set it to the same token_id that you obtain from "create tapp" in the Developer Portal. It is ok to set to any token_id during testing, but make sure you do not use the same number that also used in other actors that still in used by the dev-runner. For example, if you have tutorial-v1 actor in the dev-runner local/b-node folder (the sample-actor.wasm), and you compiled the new idea-depot actor in the same folder (named actor.wasm). Although they are different in name, but if you forgot to replace token_id to 0x111111, your new actor.wasm will have the same token_id as the old tutorial-v1 sample-actor.wasm which is 0x00000. This will cause problem when the dev-runner loads both wasm actors. 

## How do I know the actors are loaded correctly?

All functions you write in the code can only take effect after the actors are loaded into dev-runner. If any reason cause the actor load fail, no doubt your app won't work. 

There is a system request called `Activate` that called after an actor loaded. So if the actor receives this request, it will write a log so that you can find this log to determine the actor is successfully loaded.

The Activite handler is in every actor. For example, in actor/impl/src/lib.rs 

````
    async fn handle(&self, _: Activate) -> Result<()> {
        let list = [&map_fn_list()[..], &crate::dfn::name_list()[..]].concat();
        register_adapter_http_dispatcher(list.iter().map(|v| v.to_string()).collect()).await?;
        info!("activate idea vote actor successfully");
        Ok(())
    }
````

So if you can see the log "active idea vote actor successfully" in the docker log, you will know at least your actor is loaded and response to the Activate request.

If you cannot find such log, you would go to check if the actor.wasm is correctly compiled and copied to the dev-runner/local/b-node (or a-node for state actor). If the wasm is signed correctly using the key.pem. 

If you start dev-runner using  `docker-compose up`, all docker contiainers will send output to the same terminal. Sometimes it is hard to search. You can use `docker-compose up -d` to start docker as daemon. In this case, you will need to learn some docker command to read logs. Please go to docker.com to learn more. 

## Initialization

If you test it locally using dev-runner, you will need to manually intiialize token and database from the admin page. When deploy your app to testnet or mainnet, you will do so in the deployment step in Developer Portal. In your local test, you will need manually click the two buttons in the admin page. If you forget to do so, you will get error message in almost every click (e.g. login, or faucet).

## What? No_wallet?

If you see this "no_wallet" on the UI, you have not setup Metamask browser extension correctly. 



Please install Metamask browser extension then refresh your web page. It should be alright.

## Forgot to add some fund or set spending limit

If you test it locally uring dev-runner, you will need to get some free test token from the faucet button in the account page. This is built-in feature to simplify your testing experience. In the real work, there is no such faucet. You will need to somehow get some token and top up to your account before you can use your own app. 

If you do not have enough balance but run some task that costs some TEA, you will get error message such as `.... cross-move failed....from .... to .... balance ... overdraft` or something similar. When you read this message, you should know you will need more token in your balance.

Besides some token in your account balance, you will also need to allow this app to use a certain amount of token, this is called set spending limit. Fail to do so will also cause similar error message, because the app will be rejected when trying to run some transactions that costs fund but do not have enough spending limit.

## Still struggling? Do not give up, take this shortcut

If you are still struggle with all the troubles after try all your can do. Please do not give up, let's take a shortcut to the final stage. 

You can go to https://github.com/tearust/idea_depot/tree/step6 clone code from there and checkout to step6. This is what the code should look like at the end of step5. 
