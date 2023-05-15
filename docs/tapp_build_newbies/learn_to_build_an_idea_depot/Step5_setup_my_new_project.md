# Step5_setup_my_new_project

## Setup new project folder

In my last step, I have successfully run the tutorial's reward branch. Now I am going to setup a new project that use all code but with new name. 

Alternatively I can reuse the same tutorial-v1 folder, but this may cause potential problem if I forget and try to submit my changes to git. So it would be better to create a new folder for this new project.

So I will copy the code to a new folder called `mytest`. In order to make sure the build.sh can copy the wasm file to the correct folder inside dev-runner, I would make sure these 3 folders (mytest, totorial-v1, dev-runner) are at the same level. 

I copy all files from tutorial-v1 to mytest: `cp -r tutorial-v1 mytest`.This command will create mytest folder as expected. Since this is a new project, I need to remove the `.git` folder so that all git record original connecting to tutorial-v1 will be removed. 

````
cd mytest
rm -rf .git
````

Now this folder has nothing to do with tutorial-v1 git repo. If you want to use git to track changes, you can `git init` to create new git repo.

## Simplify the folder name

Just a personal preference, I think the sample-actor is too long. I rename it to actor. So to the state-actor, front-end folder. No other reason just lazy.

````

mv sample-actor actor
mv sample-txn-executor state-actor
mv sample-front-end front-end
````

## Update manifest using new token_id actor_id

We have not modify any code yet, if we build this project and try to deploy it, it will fail. Because its actor name and token_id have not been changed.  They are defined in the manifest.yaml file in both actors folders. We have to rename it to our own name, otherwise the system will consider this actor is an update version of tutoria-v1 instead of a new idea-vote application. 

I rename the actor_id and owner_id, token_id, and token_id as the following
for the manifest.yaml inside actor folder:

````
actor_id: idea-vote-actor
owner_id: com.developer
token_id: 1111111111111111111111111111111111111111
````

for the manifest.yaml inside state-actor folder

````
actor_id: idea-vote-state-actor
owner_id: com.developer
token_id: 1111111111111111111111111111111111111111
````

### actor_id

Of course, you can set to a total different name to your actor_id. But usually we will call the actor running on hosting node "something_actor", and the actor running on state maintainer node "something_state_actor". These actor_ids are used to identify actors when deploying to the TEA Project. Try to make it meaningful so that everyone knows which TApp these actors are for.

### owner_id

The owner_id need to be remaining "com.developer". This is considered a category instead of a owner_id. This is used by identify if this actor is a system actor (developed by the TEA core team) or a community application. Only TEA core team can sign and update the system actors. This owner_id is reserved for future usage for partners or other special purpose. So, again, leave it as it as "com.developer". 

### token_id

During development and if you only test it in your local simulator, the "dev-runner", you can set the token_id to anything as long as you do not have other actors under development using the same token_id and are loaded to the same dev-runner. In my case, I set it to 1111.... because the previous tutorial-v1 set it to 00000.... If you are making some other tapp at the same time, feel free to set its token_id to 222222.... 

However, if you are ready to deploy your TApp to the TEA Project (testnet or mainnet), this token_id need to be the **real** token_id you generated in the Developer Portal for your app. The detail of deployment is explaned in the tutorial under deployment session. Fail to set the the correct token_id may cause:

* Deployment failed due to duplicated token_id
* You cannot receive payments from your users
* You cannot topup to pay to support your TApp's cost. If your TApp runs out of balance, it will be removed from the TAppStore.

So please make sure you know that token_id is and set it correctly before deployment.

## General your own key.pem as developer's private key

You may ask how could the TEA Project mistakenly identify me to be the owner of tutorial-v1? Good question, remember the key.pem file? This is consider to be the private key of the developer. This key.pem file SHOULD NOT be included in the github code repo. But why we can see it in the code base. Well, this is a trade off. Most new learner want to have a download-and-compile experience. If they download a code but asked them to generate a key.pem before compile, I bet most of them will stuck or even walk away. So we keep this file in the code repo and allow anyone who download the code to get it setup and compile, test run without block. However, when you are going to make your own project, you NEED TO GENERATE YOUR OWN key.pem file. 

 > 
 > !!! George please link to our existing document on how to generate key.pem !!! and explain here.

Since I am not going to deploy mytest project, I would keep the existing key.pem to minimize my work load.

 > 
 > FAQ: Where does the owner_id and actor_id or key.pem get checked?
 > Answer: When a new actor deployed to the TEA Project, the TAppStore will verify the owner_id with existing owner_id database. If you are a return customer, then the key.pem need to be checked to ensure you are the same return developer. If you are the same return developer and you upload an actor with the same actor_id, this is considered to be an update of existing actor. At this moment, we have not provided upgrade/migration feature yet. A new actor will be considered a new actor regardless the same actor_id.

## Rename package names in Cargo.toml

In both actor and state-actor folder, you will need to rename the package name. This is what the compiled wasm file name supposed to be for those package `crate-type=["cdylib"]` in Cargo.toml. Although it doesn't make much difference when deploy, but it helps you figure out this wasm file is compiled for your project. You will be happy seeing idea-vote-actor.wasm instead of a old-looking sample-actor.wasm in the dev-runner/local/b-node folder.

I renamed actor/codec/Cargo.toml 

````
[package]
name = "idea-vote-actor-codec"
version = "0.1.0"
edition = "2021"
````

The codec package is not a cdylib, so it will not get compiled into a standalone idea_vote_actor_codec.wasm file, instead, it is included into other package (e.g. idea-vote-actor inside the impl folder). 

For actor/impl/Cargo.toml, we rename the package name to `idea-vote-actor`. Note, this package is a  `crate-type=["cdylib"]`, so it will be compiled to idea_vote_actor.wasm file when build.

Similarly, the state-actor/codec/Cargo.toml to `idea-vote-state-actor-codec`, the state-actor/impl/Cargo.toml to `idea-vote-state-actor`.

As long as we changed those two package names, all dependencies need to update as well. For example, in `actor/impl/Cargo.toml`, the dependencies need to update to the new package name. This is the final version. Note the first two lines under dependencies.

````
[package]
name = "idea-vote-actor"
version = "0.1.0"
edition = "2021"

[lib]
crate-type = ["cdylib"]

[dependencies]
idea-vote-actor-codec = { path = "../codec" }
idea-vote-state-actor-codec = { path = "../../state-actor/codec" }
tea-sdk = { workspace = true, features = ["wasm"] }
thiserror = { workspace = true }
serde_json = { workspace = true }
log = { workspace = true }
prost = { workspace = true }
primitive-types = {workspace = true}
serde = { workspace = true }
serde_derive = { workspace = true }

[dev-dependencies]
tokio = { workspace = true, features = ["full"] }
````

The final version of `state-actor/impl/Cargo.toml`. Please note the package name and the first line under dependencies.

````
[package]
name = "idea-vote-state-actor"
version = "0.1.0"
edition = "2021"

[lib]
crate-type = ["cdylib"]

[dependencies]
state-actor-codec = { path = "../codec" }
tea-sdk = { workspace = true, features = ["wasm"] }
thiserror = { workspace = true }
serde_json = { workspace = true }
log = { workspace = true }
base64 = { workspace = true }
prost = { workspace = true }

[dev-dependencies]
tokio = { workspace = true, features = ["full"] }

````

## Rename file in build.sh

Because we changed the compiled wasm file name, we also need to change the scripts in build.sh. Otherwise, the build.sh will still looking some sample-actor.wasm and sample-txn-executor.wasm.  Please use the new names idea_vote_actor.wasm and idea_vote_state-actor.wasm.

## Rename the reference package name in source file.

You still cannot build at this moment. If you try to run ./build.sh, you will notice a lot of compiling error. That is normal, because there are so many places in the code reference the old "sample-actor" or "SampleActor" name. Because we no longer have such name, it will fail in compiling. You can run a global search in your IDE, you will find a lot of SampleActor or sample-actor. You will need to replace them all with the new name. Since there are too many of them, I am not going to list all of them in this article. Please do this on your own until the tough Rust compiler satisifed. I know it will take a while, but it is what a developer is supposed to do.

Some nodes need to be mentioned here.

The NAME const value need to be changed from 
`pub const NAME: &[u8] = b"someone.sample";` to
\`pub const NAME: &\[u8\] = b"com.developer.idea-vote-actor"; Because the name of package changed. Similarly the same apply to another actor.

While updating the code, you can also remove some code that only needed for the sample-actor, such as the hello world GreetingRequest or AddRequest etc. They are no longer needed in the new idea-depot app.

## build two actors

Run ./build.sh in both actor folder and state-actor folder. Very likely you get some compiler error, then fix them one by one. Mostly caused by the renaming.

If you can successfully build those two actors, you should see them under the dev-runner/local/a-node and b-node folders.

## How about the front end?

Right now, the front end code is still the tutorial-v1 reward branch. All the front end code will still trying to send request to the backend looking for the token_id 00000... and actor name to sample-actor. We will need to change this to make it work correctly.

### Update .env.test .env.prod

There are two hidden files under the root of front-end folder. If you cannot find them, use `ls -a` to list all files. The `.env.test` is used to define the local test environment. The file `.env.prod` is used to define the production deployment environment.

You will need to change the original VUE_APP_TAPP_ID from the token_id of tutorial-v1 0000... to new token_id we set to 11111... 

````
NODE_ENV = dev
VUE_APP_LAYER2_URL=http://127.0.0.1:8000
VUE_APP_TAPP_ID=0x1111111111111111111111111111111111111111
````

The value of VUE_APP_LAYER2_URL tell the front end which URL it supposed to send requests to. If we run the backend locally using dev-runner, it is by default `http://127.0.0.1:8000`.

In the `.env.prod` you can leave the VUE_APP_LAYER2_URL empty, because it will by default point to the hosting node URL which the front end was downloaded from. This is the miner's IP. 

Again, when deployment to production (testnet or mainnet), the 0x11111 needs to be replace by the actualy **real** token_id you deployed to the Developer Portal.

After these modifications, after the front end starts running locally, it will use`http://127.0.0.1:8000` as the backend URL, send request to the  actor whose `token_id = 0x1111111111111111111111111111111111111111`. 

## Test run locally using dev-runner.

This is exactly the same as how you run the tutorial-v1 using dev-runner.

If you have done every correctly (I knew it is unlikely happen in the first time), you should have the backend running in docker. Then run `npm install && npm start` to start front end. In the URL showing on the npm command line output to start browser. You should see exactly the same tutorial-v1 reward branch working. You can try to test if all existing tutorial-v1 business logic runs correctly (hello world, create task etc). 

Note, we have not modify or add any new logic for our new TApp idea depot yet. We just created a new project using tutorial-v1/reward as boilerplate. 

Congratulation! You have made the first step to TApp building !

But wait ! What if you are not lucky enough to get it right? Please read the next article for troubleshooting. Do not worry, you are not alone. I bet 99.99% reader will run into some types of trouble. Stay calm and stay tuned.
