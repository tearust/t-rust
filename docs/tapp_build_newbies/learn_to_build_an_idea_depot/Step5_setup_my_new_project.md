# Step 5: Setup My New Project

## Setup new project folder

In my last step, I successfully ran the tutorial's reward branch. Now I am going to setup a new project that use the existing tutorial code but with a new name. 

Alternatively I could reuse the same tutorial-v1 folder, but this may cause potential problems if I forgetfully try to submit my changes to git. So it would be better to create a new folder for this new project.

I'll copy the code to a new folder called `mytest`. In order to make sure the build.sh can copy the wasm file to the correct folder inside dev-runner, I make sure these 3 folders (mytest, totorial-v1, dev-runner) are at the same level. 

I copy all files from tutorial-v1 to mytest: `cp -r tutorial-v1 mytest`.This command will create mytest folder as expected. Since this is a new project, I'll need to remove the `.git` folder so that all git records that connect to the original tutorial-v1 will be removed. 

````
cd mytest
rm -rf .git
````

Now this folder has nothing to do with tutorial-v1 git repo. If you want to use git to track changes, you can `git init` to create new git repo.

## Simplify the folder name

I'll simplify the folder name as a personal preference since I think the sample-actor name is too long. I'll rename all the folders like so for the sake of convenience:

````

mv sample-actor actor
mv sample-txn-executor state-actor
mv sample-front-end front-end
````

## Update manifest using new token_id actor_id

We haven't modified any code yet, but if we build this project and try to deploy it as is it will fail. This is because its actor name and token_id haven't been changed yet.  They're defined in the manifest.yaml file in both actors folders. We have to rename it to our own name, otherwise the system will consider this actor an updated version of tutorial-v1 instead of a new idea-vote application. 

I rename the actor_id and owner_id, token_id, and token_id as the following for the manifest.yaml inside of the actor folder:

````
actor_id: idea-vote-actor
owner_id: com.developer
token_id: 1111111111111111111111111111111111111111
````

And for the manifest.yaml inside the state-actor folder:

````
actor_id: idea-vote-state-actor
owner_id: com.developer
token_id: 1111111111111111111111111111111111111111
````

### actor_id

Of course, you can set up a totally different name for your actor_id. But usually we'll call the actor running on a hosting node "something_actor", and the actor running on a state maintainer node "something_state_actor". These actor_ids are used to identify actors when deploying to the TEA Project so we try to make it meaningful so that everyone knows which TApp these actors are for.

### owner_id

The owner_id should retain the existing name "com.developer". This is technically considered a category instead of an owner_id. This is used to identify if this actor is a system actor (developed by the TEA core team) or a community application. Only the TEA core team can sign and update these system actors. This owner_id is reserved for future usage such as for partners or other special purposes. So again, leave it as is: "com.developer". 

### token_id

If you'll only be testing in your local simulator using"dev-runner", you can set the token_id to anything as long as you don't have other actors under development using the same token_id loaded into the same dev-runner. In my case, I set it to 1111.... because the previous tutorial-v1 set it to 00000.... If you are making some other TApp at the same time, feel free to set its token_id to 222222.... or whatever numbering system that's easy for you to track.

However, if you're ready to deploy your TApp to the TEA Project (testnet or mainnet), this token_id needs to be the **real** token_id you generated in the Developer Portal for your app. The detail of deployment is explaned in the tutorial under the deployment session. Failure to set the the correct token_id may cause:

* Deployment failure due to a duplicated token_id.
* An inability to receive payments from your users.
* An inability to topup to pay to support your TApp's cost. If your TApp runs out of its balance, it will be removed from the TAppStore.

So please make sure you know that token_id is and set it correctly before deployment.

## Generate your own key.pem as developer's private key

You might ask how could the TEA Project mistakenly identify me as the owner of tutorial-v1? This all stems from the key.pem file which is considered to be the private key of the developer. This key.pem file SHOULD NOT be included in the github code repo. So why can we see it in the code base? Well, this is a trade off. Most new learners want to have a download-and-compile experience. If they downloaded code but it asked them to generate a key.pem before compile, some might get stuck or even walk away. So we keep this file in the code repo to allow anyone who downloads the code to get it setup and compiled, test run without block. However, when you're going to make your own project, you NEED TO GENERATE YOUR OWN key.pem file. You can learn more about [generating the key.pem file here](https://dev.teaproject.org/020_tutorial/020_hello_world/023_sample_actor_code_walkthrough#impl-folder).

Since I'm not going to deploy the mytest project, I will keep the existing key.pem to minimize my work load.

 > 
 > FAQ: Where does the owner_id and actor_id or key.pem get checked?
 > Answer: When a new actor's deployed to the TEA Project, the TAppStore will verify the owner_id with existing owner_id database. If you're a returning customer, then the key.pem ile needs to be checked to ensure you're the same returning developer. If you're the same returning developer and you upload an actor with the same actor_id, this is considered to be an update of an existing actor. At this moment, we haven't provided any upgrade or migration features yet. A new actor will be considered a new actor regardless if it's the same actor_id.

## Rename package names in Cargo.toml

In both the actor and state-actor folders, you'll need to rename the package name. This is what the compiled wasm file's name is supposed to be for those packages with `crate-type=["cdylib"]` in Cargo.toml. Although it doesn't make much difference as far as deployment, the names help you figure out if this wasm file was compiled for your project. You'll be happier seeing idea-vote-actor.wasm instead of an old-looking sample-actor.wasm in the dev-runner/local/b-node folder.

I renamed actor/codec/Cargo.toml:

````
[package]
name = "idea-vote-actor-codec"
version = "0.1.0"
edition = "2021"
````

The codec package is not a cdylib, so it won't get compiled into a standalone idea_vote_actor_codec.wasm file. Instead, it's included in another package (e.g. idea-vote-actor) inside the impl folder. 

For actor/impl/Cargo.toml, we rename the package name to `idea-vote-actor`. Note, this package is a  `crate-type=["cdylib"]`, so it will be compiled to idea_vote_actor.wasm file during build.

We similarly rename the state-actor/codec/Cargo.toml to `idea-vote-state-actor-codec` and the state-actor/impl/Cargo.toml to `idea-vote-state-actor`.

As long as we changed those two package names, all dependencies need to be updated as well. For example, in `actor/impl/Cargo.toml`, the dependencies need to update to the new package name. This is the final version. Note the first two lines under dependencies.

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

The following is the final version of `state-actor/impl/Cargo.toml`. Please note the package name and the first line under dependencies.

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

Because we changed the compiled wasm file name, we also need to change the scripts in build.sh. Otherwise, the build.sh will still be looking for the sample-actor.wasm and sample-txn-executor.wasm files. Please use the new names idea_vote_actor.wasm and idea_vote_state-actor.wasm.

## Rename the reference package name in the source file

You still cannot build at this moment. If you try to run ./build.sh, you'll notice a long list of compiling errors. That's normal, because there are so many places in the code that reference the old "sample-actor" or "SampleActor" name. Because we no longer have such a name, it will fail in compiling. You can run a global search in your IDE where you'll find a lot of SampleActor or sample-actor mentions. You'll need to replace them all with the new name. Since there are too many of them, I'm not going to list all of them in this article. Please do this on your own until the tough Rust compiler is satisifed. I know it will take a while, but it's one of the tasks a developer's supposed to do.

Some other notes need to be mentioned here.

The NAME const value needs to be changed from 
`pub const NAME: &[u8] = b"someone.sample";` to
`pub const NAME: &[u8] = b"com.developer.idea-vote-actor";` because the name of package changed. Similarly the same applies to another actor.

While updating the code, you can also remove some code that's only needed for the sample-actor, such as the hello world GreetingRequest or AddRequest etc. They're no longer needed in the new idea-depot app.

## build two actors

Run ./build.sh in both the actor and state-actor folders. Very likely you get some compiler errors which you'll need to fix one by one. These are mostly caused by the renaming.

If you can successfully build these two actors, you should see them under the dev-runner/local/a-node and b-node folders.

## How about the front end?

Right now, the front end code is still the tutorial-v1 reward branch. All the front end code will still try to send request to the backend looking for the token_id 00000... and actor name to sample-actor. We'll need to change this to make it work correctly.

### Update .env.test & .env.prod

There are two hidden files under the root of the front-end folder. If you cannot find them, use `ls -a` to list all files. The `.env.test` file is used to define the local test environment. The file `.env.prod` is used to define the production deployment environment.

You'll need to change the original VUE_APP_TAPP_ID from the token_id of tutorial-v1 0000... to new token_id we set to 11111... 

````
NODE_ENV = dev
VUE_APP_LAYER2_URL=http://127.0.0.1:8000
VUE_APP_TAPP_ID=0x1111111111111111111111111111111111111111
````

The value of VUE_APP_LAYER2_URL tells the front end which URL it's supposed to send requests to. If we run the backend locally using dev-runner, it's by default `http://127.0.0.1:8000`.

In the `.env.prod` you can leave the VUE_APP_LAYER2_URL empty, because it will by default point to the hosting node URL which the front end was downloaded from. This is the miner's IP. 

Again, when deploying to production (testnet or mainnet), the 0x11111 needs to be replace by the actualy **real** token_id you deployed to the Developer Portal.

After these modifications, after the front end starts running locally, it will use`http://127.0.0.1:8000` as the backend URL, and send requests to the  actor whose `token_id = 0x1111111111111111111111111111111111111111`. 

## Test run locally using dev-runner.

The step to run the TApp locally is exactly the same as how you'd run the tutorial-v1 using dev-runner.

If you've done everything correctly (and it might take you a few times), you should have the backend running in docker. Then run `npm install && npm start` to start the front end from your front-end folder. Use the URL showing on the npm command line output to launch the TApp in your browser. You should see exactly the same tutorial-v1 reward branch working. You can try to test if all existing tutorial-v1 business logic runs correctly (hello world, create task etc). 

Note that we haven't modified or added any new logic for our new TApp idea depot yet. We just created a new project using tutorial-v1/reward as a boilerplate. 

Congratulations, you've just taken your first step into the world of building TApps!

If you have errors and aren't celebratng yet then please read the next article for troubleshooting. Don't worry, you're not alone. I bet 99.99%  of readers will run into some type of trouble. Stay calm and stay tuned.
