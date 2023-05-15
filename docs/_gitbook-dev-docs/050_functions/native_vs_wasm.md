# Native vs Wasm Functions

TEA Project function calls can be either Wasm functions or native functions.

* Wasm actors can be written by developers to run in both the hosting CML nodes and the state machine nodes. Developers will need to write two functions: one for the CML hosting nodes to handle the end-user requests and another for the state machine to handle the txn requests coming from the hosting nodes. The billing costs to run Wasm actors can be calculated exactly which means CML hosts will be paid precisely according to the resources that the function uses.
* An app can call native functions to communicate with the underlying TEA system. Native actors can run in either CML or state maintainer nodes and pay out to either entity accordingly.

## Native functions

* System-level functions.
* Can only be written by TEA Core Team.
* Executed as operating system native code running on bare metal (not virtualization). Code is run directly in the CPU after being compiled into CPU instructions specific for AMD64, ARM64, x86 etc.
* Gas charge is pre-estimated by tea-codec function before execution.&#x20;
* No real-time gas measurement, and no way to break from the function if the gas limit runs out.

## Wasm function

* Application-level functions.
* Can be written by any developer.
* Compiled to WebAssembly (Wasm) code, executed inside Wasm runtime.
* Capable of real-time gas measurement. Will break and return if gas limit runs out.

Devs can only write Wasm functions, and that’s ultimately the extent of what endusers can interact with. Although the apps they write can call native functions, developers can’t write native functions (system functions) because of the security risks.

## Billing for Functions

We can measure how much computation is being done by WASM function, and the WASM function cost can be deducted from the gas limit in real time. The gas limit encompasses the entire call stack, i.e. the entire chain of functions called. Once the gas limit runs out, the WASM actor will immediately return with an error code “run-out-of-gas-limit”.

We can’t currently measure the billing costs of the native function calls that a TApp makes in real-time so we must estimate them. Stated differently, TEA can’t measure itself .. but it can measure what’s running on top of it. So we estimate what each call costs based on what our “best guess” of what it costs because we can’t get real-time costs for the native functions. Because we cannot measure the real-time cost of native actors, a gas limit isn’t applicable in this case.

![](https://cdn-images-1.medium.com/max/1200/1\*PdBoSDmyFaGHpR\_0lE6ixA.png)

## Functions vs Actors

Actors are a compiled document of related functions. For more information, please visit our [FAQ](t-rust/obsidian/\_gitbook-dev-docs/050\_functions/actors-vs-functions.md).
