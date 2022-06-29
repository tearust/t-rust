As code loaded into mini-runtime and start execution, the TEA Project will meter the usage: 

* CPU usage: Measured using Wasmtime byte code
* RAM: This is the RAM used inside the execution node not the memory in global state.  Global memory has a different billing mode [Memory_tax_billing_model](Memory_tax_billing_model.md)
* Network
* Hard drive

This is similar to AWS EC2 or Lambda billing model.

If this computing happens inside a hosting CML node. The hosting node miner will get paid. 

![\]("harberger_tax/Pasted image 20220618165623.png")

If this computing happens inside the [Global_state_maintainer_nodes](Global_state_maintainer_nodes.md)

![\]("harberger_tax/Pasted image 20220618165650.png")

it is called [Txns_computing_cost](Txns_computing_cost.md) and pay to and shared by  [Global_state_maintainer_nodes](Global_state_maintainer_nodes.md)
