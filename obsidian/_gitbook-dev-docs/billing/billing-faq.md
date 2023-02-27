# Billing FAQ

## How can developers reduce the gas that their apps require?

- Optimize code. Developers can use the same coding techniques that work to [reduce gas fees on Ethereum](https://www.alchemy.com/overviews/solidity-gas-optimization). We also have a [local test environment](local-debug-environment.md) that developers can deploy to get an idea of how much memory / memory cost their actors are consuming.
- Optimize state machine memory usage. Since TApps pay a memory tax, devs can reduce the cost of running their apps by using the least amount of state memory as possible. This fee is a memory tax  separate from the gas fee. Developers might wonder if they're charged gas for the amount of cache memory utilized within CML hosting nodes. While there's a hosting actor within the hosting node that TApp's can cache memory to, the TEA Project doesn't charge for this memory usage as it's only temporary. Besides that, it's also hard to measure.
- Choose optimal native function routing. Native function calls have different gas prices associated with them. If the call stack has optional native functions, then devs can play with the gas estimates to see which routes provide them with the cheapest overall gas costs.
- Leverage different possible hardware options through function calls. A powerful GPU can potentially do in 1 line of code what a CPU takes 10,000 lines of code to perform. But if the GPU function call is only 10x more expensive, then that line of code executes at a fraction of the gas cost compared to calling the CPU. Only native functions can access hardware directly, so devs will need to use the gas estimator to find the most efficient path that leverages the CML host's hardware.

## Why is there a gas limit?

There's a default gas limit in place on TApp operations as a safeguard against code execution running up an exorbitant fee and bankrupting the enduser.

The default gas limit can be adjusted by the user and the end-user can be prompted to do so by the TApp developer should the app be resource intensive. The gas limit acts as sensible protection against edge cases. The gas limit is a soft limit and is the max that the end-user will pay to the miner in order to run that particular TApp's actor. Note that gas fees are paid by the end-user and go to the CML miners. 

There's another limit known as the **spending limit** which is set by the end-user. This governs in-app purchases that are paid to the TApp. Both options are on the same page in the TApp Store UI.

In addition to the gas limit, the TEA Project has a **fuse** which acts as an emergency break point for any of the actors. For example, an infinite loop that continues to rack up gas charges without ever completing will have to eventually break when it hits the fuse. More about the gas limit and fuses is covered in a [separate document](gas-fee-billing.md).

## Why do TApps need to pay a memory tax?

The state machine's memory is a scarce resource. It's a resource that's shared by all TApps and as one group of TApps uses more of it, that means there's less of it available for the other TApps. In order to encourage conservative usage, a memory tax is billed to the TApp based on how much memory it uses and how long it occupies space in the state machine.

Note that the Memory tax rate is not linear: as the state memory utilization increases and the remaining available memory becomes scarce, the memory tax starts to climb. The higher memory tax rate during high utilization periods will compel some applications to reduce / optimize their memory usage.