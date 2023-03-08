# Local Dev & Test Environment
TEA Project provides a local development environment to simulate the distributed running environment locally.

The local development environment (LDE) i s a docker configuration. It simulates one hosting node and one state machine node. It's running in your local computer under docker. There 's' no hardware protection, so all Remote Attestation and consensus are simulated.

## Pre-requisites
- Git
- Docker: install docker and "docker compose"
- Node.js & hardhat: please following the hardhat install tutorial [here](https://hardhat.org/tutorial)

## Prepare dummy layer1

We should prepare the simulated (local) Ethereum layer before running the tests.

First clone our layer1 contract repository:

```
git clone https://github.com/tearust/eth_layer1
```

Then `cd contracts` and run the following to start the simulated layer1:

```
npx hardhat node --hostname 0.0.0.0
```

Finally, in another terminal (and the same directory) run the following to init contracts:

```
npx hardhat --network localdocker run scripts/deploy_alpha.js
```

## Running the tests

### Prepare your custom actors
If you have any custom wasm actors needed to be loaded, please place them in the directory `local`.

### (Option 1) Run with server (non-interactive) mode
Server mode will run the tests without interactions. You won't be able use CLI in this case. 

You can run with server mode by doing the following:

```
cd single-node
docker compose up
```

(If you installed `docker-compose` please replace the `docker compose up` command with `docker-compose up`)

### (Option 2) Run the interactive mode
First run the whole docker compose:

```
cd single-cli
docker compose up
```

(If you installed `docker-compose` please replace the `docker compose up` command with `docker-compose up`)

Then enter into the client docker service using the following command:

```
docker exec -it parent-instance-client /bin/bash
```

Then use the following command to start the client manually:

```
./app
```

After all actors have initialized successfully, you can type commands to interact with the runtime:

```
libp2p id
```

For example, the above command will give you the current node's connection id.
