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