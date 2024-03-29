TEA Swap is an Automatic Market Maker exchange decentralized asset exchange (DEX) that runs an to trade any [fungible_assets](fungible_assets.md) instantly. It is not an **order book** type exchange. Instead, it runs similar to Uniswap algorithm but different in many ways:

* TEA Swap is not a smart contract, it doesn't run on blockchain. Therefore its gas fee is almost zero, and settled instantly.
* TEA Swap works with any fungible tokens, no limited to ERC20, nor blockchain based assets. Those assets could be fiat or CBDC as long as it supports [decentralized_escrow](decentralized_escrow.md).

# Features

* Peer to contract exchange using DAMM
* Instant seattlement, not the order book type exchange.
* Secured network and consensus between member central banks
* Secured gateways provide commercial bank acces
* Hardware and physical security. 

Notes:
**This application and whole infrasture is based on specially designed trusted hardware to reach the high security level**. 

# Infrastructure layers

* Application layer:  *Exchange_protocol*
* Consensus layer: *State_machine*
* Network layer: *Remote_attestation_network*
* Hardware layer: *Trusted_enclave_hardware*

Every layer rely on the underneath layer. 

## Application: The DeX using DAMM

A Bancor like application provides decentralized asset exchange. By using DAMM

* No need of order book or market maker.  Instant seattlement
* Low or zero impermenant loss

## Consensus: Time based consensus

Use secure signed timestamp from GPS or Atomic clock as roof of trust, all state machines all over the world can reach consensus without buring energy. 

This is a distributed state machine. Every state maintainers sort the incoming transaction based on verifiable timestamp. Once all nodes reach the consensus with the same sequence of transaction, they can then process the transaction independently while keep the state sync with others.

## Network layer

All nodes only connect with other node after successfully remote attestation. The communication is between the enclaves of each nodes. Anyone outside of enclaves won't get any information from the network. Any compromised nodes (failed in remote attestation) will be quantined without damage the network.

## Trusted enclave hardware

All software code runs inside hardware enclave. No information can be peeked from outside of enclave. 

The simpest hardware enclave is hardware wallet such as Ledger.

# Key concepts

Link to explanation of AMM

Links to Fungible token

When Alice try to exchange 100 ONE asset to TWO asset. She will need to put 100 ONE asset to an escrow account. This is also called "topup". Once sent to escrow account, the 100 ONE asset is controlled by TEA Swap code. However, Alice is issued 100 wrapped ONE asset in the TEA Swap. Alice can exchange the 100 wrapper ONE asset to the cooresponding amount of wrapped TWO asset. Assuming the ONE:TWO exchange rate is 1:2. Once the exchange completed, Alice will find the 200 cooresponding wrapped TWO assets in her exchange account. She can withdraw it partially or in full at any time. After withdraw, the 200 wrapped TWO asset is burned immediately as well as the same amount (200 TWO asset, not wrapped) will be transferred from TWO asset escrow account to Alice.

The previous example assumes there is no cost to simpilify the explanation. In the real use case, there will be a small cost to pay to the *liquidity_pool_provider* and *gas_fee*.

It is very important that the escrow accounts can only be controlled by the TEA Swap code. No one, including the CBDC admin can control the fund stored in the escrow account.

# Work flow

## Top up

TEA Swap can only handle assets that "topped up" to the exchange. Those assets are usually called "wrapped asset" once topup. The reversed operation is called "withdrawl", that means take the wrapped asset out to a regular asset. 

## Fill in liquidity pool

[Automatic Market Maker(AMM)](Automatic%20Market%20Maker%28AMM%29.md) is rely on liquidity pool from *liquidity_pool_provider*. Before any two assets can be exchanged, some provider need to fill in the liquidity pool. Those provider will be incentivized by earning exchange fee. They also take risk of impremenent loss.

Once the liquidity pool is setup, anyone can start trade those assets

## Submit exchange transaction

The trader can see real time exchange rate calculated by the TEA Swap algorithm. The trader submit an exchange transaction to the system. The code will calculate the fee and converted target asset amount instantly. The trader will received the wrapped target asset immedaitely.

## Withdraw

Trader can withdraw the wrapped asset from the TEA Swap. Once withdraw, the trader will receive the asset (not wrapped) in his original bank account.
