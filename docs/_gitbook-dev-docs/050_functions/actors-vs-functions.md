# Actors vs Functions

An **actor** is a compiled file in Wasm binary format that is composed of functions. The actor is loaded into the TEA min-runtime which processes the actor's request and generates a response back to the actor's handler function. 

TEA's **functions** are like a server-less version of [lambda functions](https://en.wikipedia.org/wiki/Lambda_calculus). Functions are stateless, i.e. developers can't store any state inside the functions.

If a developer needs to store the state while running a function, they have two choices:

1. They can store it in the state machine and pay the associated memory tax. If the data is important (such as token balances), then the data should be stored in a database, i.e. the state machine.

1. Wasm functions can't store the state, but they can call native functions to store the state temporarily through the hosting actor. As mentioned earlier, there's no charge for this kind of state storage because it's only temporary.
