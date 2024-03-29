# Sample Actor Code Walkthrough

Assuming you've successfully built and run the unit test, let's get into the code structure details of this sample [actor](../../z_glossary/actor.md).

### Folder structure overview

````
ls
Cargo.lock          README.md           codec               rust-toolchain.toml
Cargo.toml          build.sh            impl                target
````

Folders and their usage:

* `Cargo.lock` is a temp file generated during building. It doesn't require further inspection unless you want to check the dependency versions.
* `Cargo.toml` is the root cargo config of this project. It contains only two other workspaces: `codec` and `impl`.
* `build.sh` is the script that builds the wasm [actor](../.../../z_glossary/actor.md).
* `codec` is one of the two main workspaces. It's related to the definitions of the data structures that will be used by other modules. Consider it an "interface" definition.
* `impl` is another of the two main workspaces. It's related to the implementation of the code logic.
* `rust-toolchain.toml` defines the build environment, versions. etc
* `target` is generated during build process. It stores the compiled wasm actor and temp files.

Most of the time, you only need to work on the codec and impl folders without touching any of the other files/folders.

The `Cargo.toml` file:

````
cat Cargo.toml
[workspace]
members = ["codec", "impl"]
resolver = "2"

[workspace.dependencies]
tea-sdk = {git="https://github.com/tearust/sdk", branch="master"}
serde = { version = "1.0.152", features = ["derive"] }
serde_json = "1.0.94"
log = "0.4.17"
thiserror = "1.0.39"
tokio = "1.26.0"
````

The `rust-toolchain.toml` file:

````
cat rust-toolchain.toml
[toolchain]
channel = "nightly-2023-01-01"
components = ["rustfmt", "clippy"]
````

The `build.sh` file:

````
cat build.sh
#!/bin/bash

cd $(dirname $0)
cd impl

cargo build --target wasm32-unknown-unknown --release

if [ $? -ne 0 ]; then
  exit 1
fi


if ! command -v tas &> /dev/null
then
    cargo install tea-actorx-signer --version 0.2.0-dev.5
fi

tas ../target/wasm32-unknown-unknown/release/sample_actor.wasm
echo "copy to dev-runner"
cd ..
cp -r target/wasm32-u

````

### codec folder

Use `ls` to list the files in the `codec` folder:

````
Cargo.toml src
````

The contents of the `Cargo.toml` file inside the codec folder:

````
[package]
name = "sample-actor-codec"
version = "0.1.0"
edition = "2021"

[dependencies]
tea-sdk = { workspace = true }
serde = { workspace = true }
````

In the package section, you can modify your project name, version etc.

There are two dependencies:

* tea-sdk is the main sdk entry to all other TEA sdk modules. 
* serde is [crates.io/crates/serde](https://crates.io/crates/serde).

In our tutorial, while adding more logic to the project, we'll have more and more dependencies added into this Cargo file.

Use `ls` to list the files in the src folder:

````
error.rs lib.rs
````

Let's take a look into `error.rs` using  `cat error.rs`:

````
use tea_sdk::{actorx::error::ActorX, define_scope};

define_scope! {
    SampleActor: ActorX {
        HttpActionNotSupported;
        GreetingNameEmpty;
    }
}
````

Rust is a strong typed language which requires all error types to be defined in detail. In the code above we've defined two error types that are used in our sample-actor.

* **HttpActionNotSupported**: In this version of the code, only http GET is handled while other types may throw this error.
* **GreetingNameEmpty**: When receiving a Greeting request from the client, the logic requires a name field inside the request. This name will be used in the response string such as "Hello your-name". But if the name field is empty, the handler will throw this error.

When new errors need to be created, simply add new Error names under the `define_scrope!` macro. We'll see more examples like this in the later steps of this tutorial.

Let's take a look into `lib.rs` using `cat lib.rs`:

````
#![feature(min_specialization)]

use serde::{Deserialize, Serialize};
use tea_sdk::serde::TypeId;

pub mod error;

pub const NAME: &[u8] = b"someone.sample";

#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
#[response(())]
pub struct GreetingsRequest(pub String);

#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
pub struct AddRequest(pub i32, pub i32);

#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
pub struct AddResponse(pub i32);
````

In this file we defined three structure types:

* **GreetingRequest**: This request needs a parameter String. It could be a developer's name like Alice.
* **AddRequest**: This is another example handler that adds two i32 numbers from the input.
* **AddResponse**: Send back the result of adding two input numbers.

There is no **GreetingResponse** because we just print the "Hello Alice" to the console without any return to the client for demo purposes. In order to make the compiler happy, you would need to have the `#[response(())]` line. This tells the compiler that this request doesn't attach a response type written by the developer, but it does return a () as response.  If you don't have such a line, the compile will give you an error because it considers that you missed a response.

Request and Response are the most important concepts in the actor design, it deserve a standalone chapter to explain. Please go to the ["understand request and response" chapter](../025_understand_request_and_response/README.md) in this tutorial step to learn more.

There's another HTTP GET handler to deal with client request that returns "Hello World". We'll get into that when we walk through the `impl` workspace.

### impl folder

Let's `cd impl` and `ls`:

````
Cargo.toml    key.pem       manifest.yaml src
````

Let's look at the `Cargo.toml` file with `cat Cargo.toml`:

````
[package]
name = "sample-actor"
version = "0.1.0"
edition = "2021"

[lib]
crate-type = ["cdylib"]

[dependencies]
sample-actor-codec = { path = "../codec" }
tea-sdk = { workspace = true, features = ["wasm"] }
thiserror = { workspace = true }
serde_json = { workspace = true }
log = { workspace = true }

[dev-dependencies]
tea-sdk = { workspace = true, features = ["host"]}
tokio = { workspace = true, features = ["full"] }
````

Note the line 
`sample-actor-codec = { path = "../codec" }`.

We'll need to use the data types defined in the codec folder.

**key.pem** is a private key file that the developer of this actor knows. It's used for verification purposes by the [TEA-runtime](../../../z_glossary/mini-runtime.md) to check if the final built wasm binary is correctly signed by the original developer when upgrading. You can generate key.pem using the openssl tool: `openssl genrsa -out key.pem`. As a developer, please make sure you keep the key.pem file securely stored. Whoever has such a pem file can impersonate you to sign a malicious wasm file under your name.

Let's look at the `manifest.yaml` file with `cat manifest.yaml`:

````
actor_id: sample
owner_id: someone
token_id: 0000000000000000000000000000000000000000
access:
  - tea:adapter
````

The **manifest** is an important definition file for this actor. It's part of the binary wasm that's signed during the build. 

The **actor_id** should be a unique id for every actor. An example of a typical actor name is "com.tea_core_team.sample_for_tutorial". We'll release a naming convention later.

The **owner_id** is the developer's ETH address (H160). Make sure you input it correctly as it's used for payment.

The **token_id** is the H160 ETH address that the TApp owns. Before deployment, the developer needs to create such a TApp in the TEA developer portal first to obtain a token_id for this TApp. In our local dev-runner, it's ok to set it to 0x0, since we won't be using the Developer Portal in the local dev-runner. All actors that work for this TApp will need to use this token_id for billing purposes. It's important to match the owner of the TApp, the owner_id, to your ETH address as the developer. The Developer Portal will reject your request if there's a mismatch during deployment or upgrade.

The `access` is a public disclaimer that lists of all the other modules that this actor will communicate with. Please make sure you only claim the modules that this actor absolutely needs to communicate with, otherwise it may cause security concerns from users. For example, if an actor claims to communicate a billing related module that it's not permitted to access, the end user or reviewers would mark this as a security concern for this actor in the community. On the other hand, if this actor attempts to communicate with another module that's not listed in the `access` list, it will be rejected at runtime by the TEA security logic. 

If you `cd` into the `src` folder, you can run `ls` to see the three files:

````
error.rs lib.rs   tests.rs
````

* `error.rs` defines the Error typess
* `lib.rs` is the main entrance point for the code logic.
* `tests.rs` contains all unit tests.

Using  `cat error.rs` to view the `error.rs` file:

````
use sample_actor_codec::error::SampleActor;
use tea_sdk::define_scope;
use thiserror::Error;

define_scope! {
    Impl: SampleActor {
        HttpActionNotSupported => @SampleActor::HttpActionNotSupported;
        HttpActionNotSupported => @SampleActor::GreetingNameEmpty;
    }
}

#[derive(Debug, Error)]
#[error("Http method {0} is not supported")]
pub struct HttpActionNotSupported(pub String);

#[derive(Debug, Error)]
#[error("Greeting name is empty")]
pub struct GreetingNameEmpty;
````

Remember that we've defined `HttpActionNotSupported` and `HttpActionNotSupported` IDs in the codec project. Here we'll put them into `SampleActor` to connect those IDs to the actually structures defined right below. 

Let's use the `HttpActionNotSupported` as an example:

````
#[derive(Debug, Error)]
#[error("Http method {0} is not supported")]
pub struct HttpActionNotSupported(pub String);
````

This error has a parameter string. When it throws this error, the name of the unsupported method can be assigned as the parameter, so that the user can get a more meaningful error detail. The error string will look like "Http method POST is not supported" in case of "post".

The `lib.rs` file has all the main logic that handles requests:

````
cat lib.rs
#![feature(min_specialization)]
#![allow(incomplete_features)]
#![feature(async_fn_in_trait)]

use crate::error::GreetingNameEmpty;
use error::{HttpActionNotSupported, Result};
use sample_actor_codec::{AddRequest, AddResponse, GreetingsRequest, NAME};
use tea_sdk::{
    actors::adapter::HttpRequest,
    actorx::hooks::{Activate},
    actorx::{actor, ActorId, HandlerActor},
    serde::handle::{Handle, Handles},
    utils::wasm_actor::logging::set_logging,
    Handle, ResultExt,
};

#[cfg(not(test))]
use ::{log::info, tea_sdk::utils::wasm_actor::actors::adapter::register_adapter_http_dispatcher};

pub mod error;
#[cfg(test)]
mod tests;

actor!(Actor);

#[derive(Default, Clone)]
pub struct Actor;

impl Handles for Actor {
    type List = Handle![
        Activate,
        HttpRequest,
        GreetingsRequest,
        AddRequest
    ];
}

impl HandlerActor for Actor {
	fn id(&self) -> Option<ActorId> {
		Some(NAME.into())
	}

	async fn pre_handle<'a>(&'a self, req: &'a [u8]) -> Result<std::borrow::Cow<'a, [u8]>> {
		set_logging(false, false);
		Ok(std::borrow::Cow::Borrowed(req))
	}
}

impl Handle<Activate> for Actor {
    async fn handle(&self, _: Activate) -> Result<()> {
        #[cfg(not(test))]
        {
            register_adapter_http_dispatcher(vec!["say-hello".to_string()]).await?;
            info!("activate sample actor successfully");
        }
        Ok(())
    }
}


impl Handle<HttpRequest> for Actor {
    async fn handle(&self, HttpRequest { action, .. }: HttpRequest) -> Result<Vec<u8>> {
        log::info!("@@ aa => {:?}", action);
        match action.as_str() {
            "say-hello" => serde_json::to_vec("Hello world!").err_into(),
            _ => Err(HttpActionNotSupported(action).into()),
        }
    }
}

impl Handle<GreetingsRequest> for Actor {
    async fn handle(&self, GreetingsRequest(name): GreetingsRequest) -> Result<()> {
        if name.is_empty() {
            return Err(GreetingNameEmpty.into());
        }

        println!("Hello, {name}!");
        Ok(())
    }
}

impl Handle<AddRequest> for Actor {
    async fn handle(&self, AddRequest(lhs, rhs): AddRequest) -> Result<AddResponse> {
        Ok(AddResponse(lhs + rhs))
    }
}

````

You can leave those macros untouched by your project. Those macros are designed to simplify the code. 

First we define the actor structure:

````
#[derive(Default, Clone)]
pub struct Actor;
````

Then we list all Types that should be handled using:

````
impl Handles for Actor {
    type List = Handle![
        Activate,
        HttpRequest,
        GreetingsRequest,
        AddRequest
    ];
}
````

After this we implement those trait Handles by writing `impl HandlerActor for Actor` and fill in the code logic for each impl.

In our sample actor example, we only handle three developer-defined instances of business logic and one system request which is called `Activate`. 

Activate is called when the actor is loaded into the runtime for the first time and starts running. The default behavior is in the following code:

````
impl Handle<Activate> for Actor {
    async fn handle(&self, _: Activate) -> Result<()> {
        #[cfg(not(test))]
        {
            register_adapter_http_dispatcher(vec!["say-hello".to_string()]).await?;
            info!("activate sample actor successfully");
        }
        Ok(())
    }
}
````

These code register this actor to the http adapter because this actor will need to receive http requests from the outside world. Once registered, the http adapter (another service running outside of the enclave) will know where to dispatch the http request. 

In our case, we add a `say-hello` string vec as the **action name**. This action name will be used when handling http requests later. Beause our sample actor is so simple it doesn't have any complex logic. We simply put a "say-hello" whenever we receive an http request regardless what the request content is.  We'll see more complicated examples in the future steps of our tutorial.

Here we handle the HttpRequest:

````
impl Handle<HttpRequest> for Actor {
    async fn handle(&self, HttpRequest { action, .. }: HttpRequest) -> Result<Vec<u8>> {
        log::info!("@@ aa => {:?}", action);
        match action.as_str() {
            "say-hello" => serde_json::to_vec("Hello world!").err_into(),
            _ => Err(HttpActionNotSupported(action).into()),
        }
    }
}
````

Because we registered the http request to the "say-hello" action name, it should always get a 'Hello world!' string. 

To demonstrate a more complex case, let's imagine the request contains a parameter (the developers name). For that we have the GreetingsRequest:

````
impl Handle<GreetingsRequest> for Actor {
    async fn handle(&self, GreetingsRequest(name): GreetingsRequest) -> Result<()> {
        if name.is_empty() {
            return Err(GreetingNameEmpty.into());
        }

        println!("Hello, {name}!");
        Ok(())
    }
}
````

In this example handler, the `name` is the parameter of the request. The handler checks if it's empty then returns a GreetingNameEmpty error. If it's not empty, then print the `Hello, THE_INPUT_NAME !` to the console. That's why you can see it when you run the unit test.

We can also have have multiple parameters in one request. We have the AddRequest handler to demonstrate this:

````
impl Handle<AddRequest> for Actor {
    async fn handle(&self, AddRequest(lhs, rhs): AddRequest) -> Result<AddResponse> {
        Ok(AddResponse(lhs + rhs))
    }
}

````

This handler simply adds up two input numbers and returns the sum. 

Note, the GreetingRequest and AddRequest don't have http registered, so you cannot call them directly from the browser by sending an http request. They're only available for unit testing in our current step. We'll add more http request handlers in the future steps of this tutorial.

You can find how those requests are handled and what the expected results are from the `test.rs` source code.

Writing unit tests is very important for TEA development. 

Here's an example unit test file, `test.rs`:

````
use sample_actor_codec::{AddRequest, AddResponse, GreetingsRequest, NAME};

use crate::{Actor, error::Result};
use tea_sdk::actorx::{ActorExt, WithActorHost, ActorId};

async fn init() -> Result<()> {
	Actor::default().register().await?;
	Ok(())
}

#[tokio::test]
async fn greeting_test() -> Result<()> {
  async {
		init().await?;
		ActorId::Static(NAME).call(
      GreetingsRequest("Alice".to_string()),
    )
    .await?;
    Ok(())
	}
	.with_actor_host()
	.await
}

#[tokio::test]
async fn greeting_empty_string_should_err() -> Result<()> {
  async {
    init().await?;
    let result = ActorId::Static(NAME).call(
      GreetingsRequest("".to_string()),
    )
    .await;

    assert!(result.is_err());
    Ok(())
  }
  .with_actor_host()
  .await
}

#[tokio::test]
async fn add_test() -> Result<()> {
  async {
    init().await?;
    let AddResponse(result) = ActorId::Static(NAME).call(AddRequest(1, 2)).await?;
    assert_eq!(result, 3);
    Ok(())
  }
  .with_actor_host()
  .await
}
````
