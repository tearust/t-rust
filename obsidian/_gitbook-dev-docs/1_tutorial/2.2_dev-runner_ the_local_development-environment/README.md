## Unit test ->Dev runner ->Test net ->Production

TApp development needs 4 basic steps. 
- Unit test: Run cargo test every time after code change. This is the fastest and cheapest way to test logic.
- Dev runner: The local development environment. It is a simulator of the TEA Project runtime but running locally. Like Hardhat for Ethereum, this is the simple and fast test to verify the front end and back end logic integration.
- Test net: This is **almost** the same as the final production environment but all the assets are faked. You do not need to worry about any mistakes cause real funding losses. 
- Production: This is the last step. It runs in the real production. Please be noted, this is not a drill, any mistake in your code may cause **real** funding losses. And there is **no way** to get them back. 

In last step, we have walked through the unit test. In this step, we will walkthrough the Local Development Environment, called "Dev Runner". 

The local development environment (dev-runner) is a docker configuration. It simulates one hosting node and one state machine node. It's running in your local computer under docker. There's no hardware protection, so all Remote Attestation and consensus are simulated.

# Installation

## Pre-requisites
- Git
- Docker: install docker and "docker compose"
- Node.js

## Running the tests

### Prepare your custom actors

If you have any custom wasm actors needed to be loaded, please place them in the directory `local`. In our sample-actor case, you can find the file located at `/tearust/sample-actor/target/wasm32-unknown-unknown/release/sample_actor.wasm`, please cp this file to the `local` /b-node folder as the dev-runner will load all wasm actors inside the `local` folder. If the sample_actor.wasm not exists, you may forgot to build it. Run `./build.sh` to build it first. The build.sh should copy the wasm file to the `local/b-node` at the last step. 

### Start the docker container servers

You can run with server mode by doing the following:

```
cd single-node
docker compose up
```

(If you installed `docker-compose` please replace the `docker compose up` command with `docker-compose up`)

Please wait for all contianers start and the log showing no errors.

### Use CURL to send json http post request without front end

While the server running, open another new terminal, run 
```
curl -H "Content-Type: application/json" -d '{"actor": "someone.sample", "address": "0x0000000000000000000000000000000000000000"}' http://localhost:8000/say-hello
```

You should see the output 
```
"Hello world!"
```

Now you know the server is running and the sample actor responds as expected.

You can also use Postman or any testing tools to simulate a front end sending requests to the server. Just make sure:

- Use the following address: `localhost:8000/say-hello`
- json request
- http post
- the json request as described above

In our next session we'll create a sample-front-end project. It will run such requests in the browser to make future tutorials easier.

### Start the front end

Make sure you're in the `sample-front-end` folder.

Run `npm start` as you did in the previous step.

Open a browser and vlisit [[http://127.0.0.1:3200](http://127.0.0.1:3200/)].

You should see a page with the text "Welcome to Sample Actor testing page." and a single button "click here to send request".

Please make sure your dev-runner is up and running. If not, follow the instructions in the previous steps. If the backend is running correctly, you can click the button to send a request to the sample-actor. You should see the response in a browser alert: 

```
{"data":"Hello world!","status":200,"statusText":"OK","headers":{"content-length":"14","content-type":"application/json"},"config":{"url":"/say-hello","method":"post","data":"{\"actor\":\"someone.sample\",\"address\":\"0x000000000000000000000000000000000000000f\"}","headers":{"Accept":"application/json, text/plain, */*","Content-Type":"application/json;charset=utf-8"},"baseURL":"http://127.0.0.1:8000","transformRequest":[null],"transformResponse":[null],"timeout":0,"xsrfCookieName":"XSRF-TOKEN","xsrfHeaderName":"X-XSRF-TOKEN","maxContentLength":-1,"maxBodyLength":-1},"request":{}}
```

Congratulations! Your first TApp is correctly running in your local development environment. 

In the next step, you'll learn how to deploy it to the testnet.

## Limitation of dev-runner

Dev-runner is a docker simulator to real TEA-runtime. It is different in the following aspects:

- There is no hardware security. 
- There is only one host node and one state machine node.
- No consensus is required since there is only one state machine node.
- No real remote attestation. All nodes are "good " nodes.
- No layer 1(Ethereum) interaction. 
- All initial funding in the DAO_RESERVE account. Of course, the money is faked.

You can stop the docker at any time, and it will get back to the original state when you restart . You cannot store the state.

