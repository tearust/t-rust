# Gas & Fuse Limits

The TEA Project uses a **fuse** design to ensure that developer's code doesn't use more gas than expected (e.g. infinite loops).

In the TEA Project design, there's a soft limit known as the **gas limit** in addition to a hard limit where the **fuse** kicks in. 

- The end-user has a gas limit which they acknowledge they're responsible for when using the TApp.
- The developer has a security deposit that's charged whenever an actor uses more than the gas limit up until the fuse point where their entire security deposit is slashed.

Each actor has its own security deposit that the developer must fund. These security deposits correlate to the memory quota of the actor which is the max amount of memory that actor can use. This memory quota is set by the developer for each actor. The larger the memory quota, the higher the security deposit.

The following table summarizes what happens when these two levels, gas limit and fuse, are breached.

| Exit Stage  | Exit Status  | Payment Responsibility  |
|---|---|---|
| **Fuse** is tripped.  | Actor is terminated by the mini runtime, and memory is not released.  | Dev deposit is slashed completely when fuse is tripped.  |
| Exceeded **gas limit** but not yet reached the **fuse** level.  | Actor exits normally, memory is released.   | Developer pays for any gas amount above the gas limit.  |
| Up to & including the **gas limit**.  | Actor exits normally, memory is released.  | End-user pays for any gas amount up to the gas limit.  |
 
The developer can run their code in a [test environment](local-debug-environment.md) to get an idea of each actor’s memory usage as well as test for edge cases which cause high utilization.