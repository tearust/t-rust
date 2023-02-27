Two major parts of billing. 

## Gas fee.

TApp pays to the state machine node.
Who owns the txn entry actor function pays the gas. The measurement go all the way to the end, including the sub function called inside this txn are count on this first owner.

## Function fee.

The reason we need function fee is that we want devs who make function get compensated by function being called by others

Only for the 3rd party devs. TAppStore is always Free-of-charge to all developers. TAppStore functions set zero in their Estiamte function.

When a 3rd party actor function is called, the estimate function first check if it is called by the same owner, if yes, the fee is waived. but if it is called by other callers (not myself), the caller TApp need to pay a function fee. 

The function fee is calculated using the estimate function. This estimate function is similar to the Native function estimate. It takes the calling args, caller tokenid. then calculate how much the caller should call.

the callee (who is called) can decide to allow execution or disallow. I think the best place to determine inside the estimate function. The estimate function can return a value indicate I do not want to be called under this caller and args. 

I suggest the return value of the estimate function is `Option<u32>`  None means do not allowed to be called. u32 is the function fee. 0 means free to use.

Function fee transferred from caller to callee. This is also the standard settlement process.

## Common cases of free function fee

Most common cases of free-of-charge would be

* Caller and callee owned by the same developer
* Callee is public service, such TAppStore.
* Callee estimate function returns 0 after calculation
* 

## Will the function fee occured in state machine (A) nodes only?

No, I think as long as we allow function A calls function B, we should ask the caller to pay callee except the callee agree free of charge. 

So it should happen in hosting nodes (B) too.

## TAppStore has no funciton call income, but still need to pay gas fee

Based on the design above, TAppStore as a special public service app, it will need to pay gas to the node (state machine), but it did not receive payment from other TApps as function fee. In this case, the gas fee is paid from public service funding.

Because only the txn entry function need to pay gas. If a TAppStore function is called inside another txn, the txn pays gas, TAppStore doesn't. So only when the TAppStore is the entry txn, the TAppStore pays gas fee. Usually in this case, TAppStore will need to change InApp Purchase to cover the gas cost.

## Public service funding is paid from Distribution pool

TAppStore will not have a Global Token. It should not have Hidden_System_Account.

When there is an income, it should be (directly or indirectly) to the Distribution pool. 

When there is a expense (such as pay gas fee to state machine txn execution), it gets fund from the Distibution pool

## Distribution pool pays public service first, remaining go to Dividend

We should have a minimal-pool-balance for Distribution pool. When the fund level lower than this balance, no dividence is paid. 

The public service take fund from this distribution pool. But when the distribution pool balance gets to zero. We will pay the public service from DAO_RESERVE.

## Should we give every actor function an estimate function?

I do not think we need. If you do not think your function will be called, you do not need to write such estimate function. and you function is free to use. In other words, you do not get paid by others using your function

## Txns execution entry function owner pays the gas

这个和刚才的讨论不同，需要讨论一下。

We do not need to get context from the B nodes caller all the way to the A nodes. We only need to find out who owns the txns entry function. This owner will pay the execution fee including the internal function calls to other 3rd party actor function.

For example:

Alice write an A actor function F1. Inside this F1, it calls another F2 written by Bob.

Charlie's TApp in B node sends a txn to A node. This txn is called Alice's F1 as txn entry. F1 need to pay all gas fee in A nodes including the consumption happens inside F2. But Bob doesn't need to pay gas fee of F2, because F1 is the entry.

If Alice's F1 set an estimate of 0.1T for function fee. When Alice's B nodes actor calls her own function F1, Alice doesn't need to pay the 0.1T. But in this case, Charlie's TApp calls F1, so Charlie's Tapp needs to pay 0.1T function fee.

Bob's F2 set an estimate  0.3 T function fee. Because Alice F1 calls F2. Alice needs to pay 0.3T to Bob. These are all function fee, not gas fee.
