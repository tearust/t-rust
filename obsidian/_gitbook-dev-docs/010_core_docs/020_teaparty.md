# TEA Party TApp Intro

## The goal of TEA Party
We built the TEA Party TApp to show: 

- What a typical [web3](070_What_makes_a_Web3_application.md) looks like (we call them **TApps**).

- The building blocks of a typical TApp.

- How to use Tea Party as a boilerplate to build your own TApps.

The TEA Party TApp is a useful social media application. Users can post messages to a public board as well as send private messages with notifications. See [the TEA Party user guide](030_how_to_use_TEA_Party.md) for more information.

## Basic workflow
In this section, we'll learn the basic workflow between all three tiers: how a user action get processed from the front-end to the state machine layer and back to the user.

```mermaid
sequenceDiagram
	participant A as Front end
	participant B as Back end
	participant C as IPFS/OrbitDB
	participant D as State machine receiver
	participant E as State machine conveyor
	participant F as State machine actor
	participant G as State
	participant H as GlueSQL
	A->>A: click the Tea party URL received from shared link or wallet
	rect rgb(222,222,222)
	A->>+B: reqeust to launch the Tea Party via the url link
	B->>+C: get the CID and request IPFS
	C->>-B: resposne the html/css/js front end code
	B->>-A: send back the html/css/js front end code
	A->>A: render on the browser. show the UI to the end user.
	end
	rect rgb(222,222,222)
	A->>+B: query the NoSQL data (such as messages list without querying SQL database)
	B->>+C: query OrbitDB
	C->>-B: response data (such as message body)
	B->>-A: send message body
	A->>A: render in browser, showing to the end user
	end
	rect rgb(222,222,222)
	A->>+B: query SQL database (such as my starred message list)
	B->>+D: sending query txn
	D->>+F: dispatch query txn
	F->>+H: actor query GlueSQL
	H->>-F: response query result (such as my starred message IDs)
	F->>-D: response ids
	D->>-B: response ids
	B->>+C: query the messages body based on message IDs from GlueSQL
	C->>-B: response message bodies that I starred
	B->>-A: response message bodies that I starred
	A->>A: render on browser. 
	end
	rect rgb(222,222,222)
	A->>+B: post a new message
	B->>B: generate a command txn to pay for the fee
	B->>D: send the command txn and followup to at least two state machine replicas
	D->>E: the command txn waiting on all the conveyors
	E->>E: waiting in conveyor
	E->>+F: after the grace period, the command txn dispatch to state machine actor for execution
	F->>+G: actor execute command and call state update
	G->>G: update state inside state machine 
	G->>-F: command txn committed and state changed
	F->>-E: response back
	E->>D: response back
	D->>B: response back to hosting CML
	B->>B: confirm the payment went through, now generate post message txn to OrbitDB
	B->>+C: insert new message to OrbitDB
	C->>-B: inserted
	B->>-A: confirmed posting message completed
	A->>A: render the UI and notify end user.
	end
```

## The magical Proof of Time state machine
In this section, we'll explain how the distributed state machine works, including how it handles consensus among different replicas.
Keep reading about the [magic of the state machine](080_magic_of_state_machine.md).

## Understand WebAssembly Runtime
You can learn more about how the WebAssembly code runs inside the [[mini-runtime]] by reading about the [[magic_of_wasm]].