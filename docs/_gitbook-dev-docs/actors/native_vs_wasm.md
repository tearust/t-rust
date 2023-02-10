# Native vs Wasm Functions

## Native functions

* System-level functions.
* Can only be written by TEA Core Team.
* Executed as operating system native code running on bare metal (not virtualization). Code is run directly in the CPU after being compiled into CPU instructions specific for AMD64, ARM64, x86 etc.
* Gas charge is pre-estimated by tea-codec function before execution. 
* No real-time gas measurement, and no way to break from the function if the gas limit runs out.

## Wasm function

* Application-level functions.
* Can be written by any developer.
* Compiled to WebAssembly (Wasm) code, executed inside Wasm runtime.
* Capable of real-time gas measurement. Will break and return if gas limit runs out.
