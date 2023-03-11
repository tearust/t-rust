
## Build sample-actor

The output of the build process is a Webassembly binaray called sample-actor.wasm. This binrary executable code will be loaded into the TEA-runtime which we will talk more in future steps. Now let's focus on how to build it.

Assuming you have cloned the code and installed all dependencies as instructed in previous step. If not, please do so now. Make sure the following items have been installed correctly:
- rust nightly
- wasm32-unknown-unknown target
- protobuf

Now `cd sample-actor` and run `./build.sh` to build the actor. 

Once built successfully, you can find the wasm file `sample_actor.wasm` in the `sample-actor/target/wasm32-unknown-unknown/release` folder. Note that in this step of the tutorial, we won't deploy to the TEA Project testnet, nor run in local single node development environment. We'll get into them in later steps. In this initial step, we'll only run the unit test.

The build.sh will also try to copy the sampel-actor.wasm file to the ../../dev-runner/local/b-node folder. If you do not have that folder (yes, you do not have it yet, but you will in next step), you may get an error. that is ok. You will not see it after next step of "running local development environement" tutorial.

If you get other error message, most likely some missing pieces of your building environment. Please go back to the previous step to check again.

## Run sample-actor unit test

In this step, we will not run the sample-actor in your local development environment (called dev-runner). Instead, we just run a unit test.

You can run `cargo test` to run all the unit tests. You should see the test results as follows:

```

cargo test

Compiling sample-actor v0.1.0 (/Users/kevinzhang/github/tearust/sample-actor/impl)

Finished test [unoptimized + debuginfo] target(s) in 0.94s

Running unittests src/lib.rs (/Users/kevinzhang/github/tearust/sample-actor/target/debug/deps/sample_actor-1495a49ca0ae2c13)

running 3 tests

Hello, Alice!

test tests::add_test ... ok

test tests::greeting_empty_string_should_err ... ok

test tests::greeting_test ... ok

test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

```


When you want to test a request handler and expect to return an error, see the following example:

```

async fn greeting_empty_string_should_err() -> Result<()> {

let result: Result<_> = call(

RegId::from(Actor::NAME).inst(0),

GreetingsRequest("".to_string()),

)

.await;

assert!(result.is_err());

Ok(())

}

```


You should see that the tests have passed. 

We highly recommend to write and run a unit test whenever you add / modify code. Building and deploying onto the testnet usually will take much longer than simply running a local unit test.

## Run sample-front-end

First  from code root `cd sample-front-end`, then run `npm install` to install dependencies.

If your backend has different IP or port number other than the default localhost:8000, please edit the `.env.test` file to edit in your customized values:

```
VUE_APP_LAYER2_URL=http://127.0.0.1:8000
```

This address will be your backend service address. 
Start front end local web server by run `npm start`

If you can see 
```
  App running at:
  - Local:   http://localhost:3200/
  - Network: http://192.168.1.10:3200/

  Note that the development build is not optimized.
  To create a production build, run npm run build.
```
then you front end is up running. 

At this moment, you cannot send request to backend and get "hello world" yet. We will get into the local development environment in next step.

You can Ctrl + C to stop the front end now.

