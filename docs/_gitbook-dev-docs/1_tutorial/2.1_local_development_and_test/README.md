# Local Dev & Test Environment

TEA Project provides a local development environment to simulate the distributed running environment locally.

The local development environment (LDE) is a docker configuration. It simulates one hosting node and one state machine node. It's running in your local computer under docker. There's no hardware protection, so all Remote Attestation and consensus are simulated.

## Pre-requisites

* Git
* Docker: install docker and "docker compose"
* Node.js & hardhat: please following the hardhat install tutorial [here](https://hardhat.org/tutorial)

## Prepare dummy layer1

We should prepare the simulated (local) Ethereum layer before running the tests.

First clone our layer1 contract repository:

````
git clone https://github.com/tearust/eth_layer1
````

Then `cd contracts` and run the following to start the simulated layer1:

````
npx hardhat node --hostname 0.0.0.0
````

Finally, in another terminal (and the same directory) run the following to init contracts:

````
npx hardhat --network localdocker run scripts/deploy_alpha.js
````

## Running the tests

### Prepare your custom actors

If you have any custom wasm actors needed to be loaded, please place them in the directory `local`. In our sample-actor case, you can find the file located at `/tearust/sample-actor/target/wasm32-unknown-unknown/release/sample_actor.wasm`, please cp this file to the `local` folder as the dev-runner will load all wasm actors inside the `local` folder. If the sample_actor.wasm not exists, you may forgot to build it. Run `./build.sh` to build it first.

### Start the docker container servers

You can run with server mode by doing the following:

````
cd single-node
docker compose up
````

(If you installed `docker-compose` please replace the `docker compose up` command with `docker-compose up`)

Please wait for all contianers start and the log showing no errors.

### Use CURL to send json http post request

While the server running, open another new terminal, run 

````
curl -H "Content-Type: application/json" -d '{"actor": "someone.sample", "address": "0x0000000000000000000000000000000000000000"}' http://localhost:8000/say-hello
````

You should see the output 

````
"Hello world!"
````

Now you know the server is running and the sample actor responds as expected.

You can also use Postman or any testing tools to simulate a front end sending requests to the server. Just make sure:

* Use the following address: `localhost:8000/say-hello`
* json request
* http post
* the json request as described above

In our next session we'll create a sample-front-end project. It will run such requests in the browser to make future tutorials easier.
