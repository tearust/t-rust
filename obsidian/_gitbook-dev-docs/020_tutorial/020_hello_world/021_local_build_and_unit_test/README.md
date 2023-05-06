# Step 1: Build sample-actor and Run Unit Test
## Build sample-actor

The output of the build process is a Webassembly binary called `sample-actor.wasm`. This binary executable code will be loaded into the [TEA-runtime](../../../z_glossary/mini-runtime.md) which we'll talk about more in future steps. Now let's focus on how to build it.

Assuming you have cloned the code and installed all dependencies as instructed in previous step. If not, please do so now. Make sure the following items have been installed correctly:

- rust nightly
- wasm32-unknown-unknown target
- protobuf

Now `cd sample-actor` and run `./build.sh` to build the actor. 

Once built successfully, you can find the wasm file `sample_actor.wasm` in the `sample-actor/target/wasm32-unknown-unknown/release` folder. Note that in this step of the tutorial, we won't deploy to the TEA Project testnet, nor run in local single node development environment. We'll describe them more in later steps. In this initial step, we'll only run the unit test.

The **build.sh** script will also try to copy the **sample-actor.wasm** file to the `~/local/b-node` folder of the `dev-runner` repo. You'll get an error because you don't have that folder yet, but you'll create that folder in the next step of "running local development environment" tutorial.

If you get another error message, it's most likely due to some missing pieces in your development environment. Please go back and check for missing steps.

## Run sample-actor unit test

In this step, we won't run the sample-actor in your local development environment (called dev-runner). Instead, we'll just run a unit test.

You can run `cargo test` to run all the unit tests. You should see the test results as follows:

```
cargo test

running 3 tests
test tests::greeting_test ... ok
test tests::greeting_empty_string_should_err ... ok
test tests::add_test ... ok

test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

     Running unittests src/lib.rs (target/debug/deps/sample_actor_codec-0cfabb9e4ae1c025)

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

   Doc-tests sample-actor-codec

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s

```

When you want to test a request handler and expect it to return an error, you can use the following example:

```

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

```

You should see that the tests have passed. 

We highly recommend to write and run a unit test whenever you add / modify code. Building and deploying onto the testnet usually will take much longer than simply running a local unit test.

## Run sample-front-end

First  from the root of the code repo, `cd sample-front-end`. If your backend has different IP or port number other than the default localhost:8000, please edit the `.env.test` file to edit in your customized values:

```
VUE_APP_LAYER2_URL=http://127.0.0.1:8000
```

This address will be your backend service address. 

Run the following  to install dependencies:
`npm install`
Then start the frontend local web server by running:
`npm start`

If you can see the following:

```
  App running at:
  - Local:   http://localhost:3200/
  - Network: http://192.168.1.10:3200/

  Note that the development build is not optimized.
  To create a production build, run npm run build.
```

then your front end is up running. 

At this moment, you cannot send requests to the backend and get "hello world" message yet. We'll get into the local development environment in the next step.

You can send `Ctrl + C` to stop the frontend now.

