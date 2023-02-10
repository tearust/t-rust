# Function Calls Between Native & Wasm

## Call vs Post

There are two ways to call another function from either Native and Wasm functions.

* **Call**: Sync call. The caller will wait for the subroutine to complete and return before continuing.
* **Post**: Async call. The caller will not wait for the subroutine to complete, i.e. the subroutine will run separately from the caller.

## Native function gas estimate function in Tea-Codec

Every native function will have a gas-estimate helper function that stays inside the Tea-codec. This function will run logic to estimate how much gas the function run will cost. Although there's no way to precisely measure the cost, this function will be used to get the best estimate. 

When a caller calls/posts another function, the calling activity is measured as **estimated cost deducted from gas limit At time of call**. The system will run the **Gas Estimate** function first. If the caller cannot afford this cost (exceeds the gas limit), the function call will fail with error "Gas limit ran out". If the gas limit can cover the cost, the estimated gas cost will immediately be deducted from the calling function's gas limit. This will guarantee that the bill can get paid at the time of settlement.

## How gas is measured in Native/Wasm \<\> Call/Post combinations

|Call Type |How Gas is Charged |
|-----------|--------------------|
|Native call Native |Free of charge. |
|Native call Wasm |Forbidden. |
|Native post Native |Free of charge. |
|Native post Wasm |  Native runs gas estimate function to estimate gas cost it must budget for. This gas budget is then reduced from gas limit at time of call.|

A Native call or post to Native is free because Native can't run the estimate function. Even if it could estimate the gas cost, it wouldn't being able to alter the gas limit of the Native function calling it.

|Call Type |How Gas is Charged |
|-----------|--------------------|
|Wasm call Native |Estimated cost deducted from gas limit at the time call starts. |
|Wasm call Wasm |Measured by Wasmer as real time usage. |
|Wasm post Native |Estimated cost deducted from gas limit at the time call starts. |
|Wasm post Wasm|Native runs gas estimate function to estimate gas cost it must budget for. This gas budget is then reduced from gas limit at time of call. |
