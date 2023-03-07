## Sample actor introduction

https://github.com/tearust/sample-actor is the hello world boilerplate for TApp development. In stead of building your own app from ground up, you can clone this boilderplace and build your own logic upon. In our tutorial, we will start with this boilerplate then build our own tapp. In this lesson, you will learn:
- how to clone boilerplate repo
- how to build wasm actor
- how to run unit test
- the standard code structure of tapps
- detail code walkthrough

In this sample actor, we will build a sample wasm actor that serve as a web service. It can response to a GET http request then return a "hello world" string. It also has a AddRequest to add two input numbers, but this request is not hooked up with any http request so you cannot try it from your browser. You can see it work from the unit test though.

## Clone and Rename Project

Run `git clone git@github.com:tearust/sample-actor.git` to clone the boilerplate to local. You can also rename the project to your own project name so that you can publish to your own repo in the future. Please read the [[#Sample actor code walkthrough]] to replace the project name in the two`Cargo.toml` files.

## Build wasm actor

Run `./build.sh` to build the wasm actor. If you have not installed wasm32-unknown-unknown target, or not the nightly version, you might be prompted to `rustup` install it. Just follow the command line instruction.

Once build successfully, you can find the wasm file `sample_actor.wasm` under `sample-actor/target/wasm32-unknown-unknown/release` folder. Note, in this step of the tutorial, we will not deploy to TEA Project testnet, nor run in local single node development environment. We will get into them in later steps. In this step, we will only run unit test.

## Run unit test

Run `cargo test` will start run the unit test. You should see tests passed. We high recommend to write unit test and run unit test whenever you add / modify code. Building and deploy into testnet may take much longer time than simply run local unit test.

## Sample actor code walkthrough

Assumming you have successfully build and run the unit test. Let's get into the detail of the code structure of this sample actor.

### Folder structure overview
```
ls -l
total 216
-rw-r--r--  1 kevinzhang  staff  97113 Mar  6 20:25 Cargo.lock
-rw-r--r--  1 kevinzhang  staff    198 Mar  6 20:25 Cargo.toml
-rwxr-xr-x@ 1 kevinzhang  staff    293 Mar  6 17:51 build.sh
drwxr-xr-x@ 4 kevinzhang  staff    128 Mar  6 17:51 codec
drwxr-xr-x@ 6 kevinzhang  staff    192 Mar  6 17:51 impl
-rw-r--r--@ 1 kevinzhang  staff     78 Mar  6 17:51 rust-toolchain.toml
drwxr-xr-x@ 7 kevinzhang  staff    224 Mar  6 17:52 target
```

Folders and their usage:
- Cargo.lock is a temp file generated during building. We can ignore it unless you want to check the version of dependencies.
- Cargo.toml is the root cargo config of this project. It contains only two other workspaces: codec and impl
- build.sh is the scripts that build the wasm actor
- codec is one of the two main workspaces. It is related to the definitions of data structures that will be used by other modules. Consider it as an "interface" definition.
- impl is another of the two main workspaces. It is related to the implementation of the code logic.
- rust-toolchain.toml defineds the building environment, versions. etc
- target is generated during building process. It stored the compled wasm actor and temp files.

In most time, you only need to work on the codec and impl folders without touch all other files/folders

Cargo.toml
```
cat Cargo.toml
[workspace]
members = ["codec", "impl"]
resolver = "2"

[workspace.dependencies]
tea-sdk = "0.2.0-dev.1"
serde = { version = "1.0.152", features = ["derive"] }
thiserror = "1.0.39"
tokio = "1.26.0"
```
rust-toolchain.toml
```
cat rust-toolchain.toml
[toolchain]
channel = "nightly-2023-01-01"
components = ["rustfmt", "clippy"]
```
build.sh
```
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
    cargo install tea-actorx-signer --version 0.2.0-dev.1
fi
```

### codec folder

List the files in codec folder
```
ls -l
total 8
-rw-r--r--@ 1 kevinzhang  staff  149 Mar  6 17:51 Cargo.toml
drwxr-xr-x@ 4 kevinzhang  staff  128 Mar  6 17:51 src
```

Cargo.toml inside codec folder
```
[package]
name = "sample-actor-codec"
version = "0.1.0"
edition = "2021"

[dependencies]
tea-sdk = { workspace = true }
serde = { workspace = true }
```

Under package section, you can modify your project name, version etc.

There are two dependencies, 
- tea-sdk is the main sdk entry to all other TEA sdk modules. 
- serde is https://crates.io/crates/serde

In our tutorial, while adding more logic into the project, we will have more than more dependencies added into this Cargo

Files in src folder
```
ls -l
total 16
-rw-r--r--@ 1 kevinzhang  staff  173 Mar  6 17:51 error.rs
-rw-r--r--@ 1 kevinzhang  staff  416 Mar  6 17:51 lib.rs
```

Let's take a look into error.rs
```
cat error.rs
use tea_sdk::{actorx::runtime::error::Runtime, define_scope};

define_scope! {
    SampleActor: Runtime {
        HttpActionNotSupported;
        GreetingNameEmpty;
    }
}
```
Rust is a strong typed language. It is very important to define all error type in detail. In the code above we defined two error types that used in our sample-actor.
- HttpActionNotSupported: In this version, only http GET is handled, other types may throw this error.
- GreetingNameEmpty: When receiving Greeting request from the client, the logic require a name field inside the request. This name will be used in the response string such as "Hello your-name". But if the name field is empty, the handler will throw this error.

When new errors need to be created, simply add new Error name under the `define_scrope!` macro. We will see more examples like this in our later steps of this tutorial.

Let's take a look into lib.rs
```
cat lib.rs
#![feature(min_specialization)]

use serde::{Deserialize, Serialize};
use tea_sdk::serde::TypeId;

pub mod error;

#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
#[response(())]
pub struct GreetingsRequest(pub String);

#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
pub struct AddRequest(pub i32, pub i32);

#[derive(Debug, Clone, Serialize, Deserialize, TypeId)]
pub struct AddResponse(pub i32);
```

In this file we defined three struct types
- GreetingRequest: This request need a parameter String. It could be developers name like Alice.
- AddRequest: This is another example handler that add two i32 numbers from input
- AddResponse: Send back the result of adding two input numbers.

There is no GreetingResponse because we just print the "Hello Alice" to the console without return to the client for demo purpose. There is another HTTP GET handler to deal with client request then returns "Hello World". We will get into it when walk through the `impl` workspace.

### impl folder

```
ls -l
total 24
-rw-r--r--@ 1 kevinzhang  staff   364 Mar  6 17:51 Cargo.toml
-rw-r--r--@ 1 kevinzhang  staff  2459 Mar  6 17:51 key.pem
-rw-r--r--@ 1 kevinzhang  staff   110 Mar  6 17:51 manifest.yaml
drwxr-xr-x@ 5 kevinzhang  staff   160 Mar  6 20:38 src
```

Cargo.toml
```
cat Cargo.toml
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

[dev-dependencies]
tea-sdk = { workspace = true, features = ["mock"] }
tokio = { workspace = true, features = ["full"] }
```

Note the line 
`sample-actor-codec = { path = "../codec" }`,
we will need to use the data types defined the codec folder.

During development, we will use Mock for unit testing. So you can see the line
`tea-sdk = { workspace = true, features = ["mock"] }` under the dev-dependencies section. The mock is a fake tea runtime that load the testing wasm actor and run the unit test functions in your dev machine without deplying to the testnet.

**key.pem** is a private key file that the developer of this actor knows. It is used to verify if the final built wasm binary is correctly signed by the original developer by the TEA-runtime when loading. As a developer, please make sure you keep the key.pem file securely stored. Whoever has such pem file can imperonate you to sign a malicious wasm file under your name.

Mnifest.yaml

```
cat manifest.yaml
actor_id: sample
owner_id: someone
token_id: 0000000000000000000000000000000000000000
access:
  - tea:adapter
```

Manifest is an important definition file to this actor. It is part of the binary wasm that signed during build time. 

actor_id is an unique id for every actor. A typical actor is is "com.tea_core_team.sample_for_tutorial". We will release a naming convention later.

owner_id is the H160 address that the developer's ETH account. This is used for payment. Make sure you input it correctly.

token_id is the H160 address that the TApp own. Before deployment, the developer need to create such TApp in the TEA develolper portal first to obtain a token_id for this TApp. All actors work for this TApp will need to use this token_id for billing purpose. It is important to match the owner of the TApp, the owner_id, your ETH address as the developer. The Developer Portal will reject your request when mismatching during deployment or upgrade.

access is the list of all other modules this actor will communicate with. This is a disclamer to the public. Please make sure you only claim the modules that this actor is absolutely need to communicate with, otherwise it may cause security concerns from users. For example, if an actor claims to communicate a billing related module but not supposed to, the end user or reviewers would mark a security concern to this actor in the community. On the other hand, if this actor attempt to communicate with another module that not listed in the `access` list, it will be rejected at runtime by the TEA security logic. 

