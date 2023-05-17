The idea dump for https://hackolosseum.apixplatform.com/h1/g20techsprint2023#preview G20 TechSprint 2023

## Highlights

Not a blockchain, high scalability
Confidential, hardware security. Unless specific open AI, no one know what happens inside. 
By providing a tx detail, system API can response if this txn has been processed and when.
Full decentralized, high resilience to attack. It is unlikely to tear down as long as very few nodes survive from disaster. 


## System setup

Every country join the peer to peer network by providing a few nodes. Invitation only.
AWS Nitro is used at the beginning.

## Scope and 3rd party assumption

### CBDC API

Support multisig. The sign parties hold their partial private keys and sign using (ideally) snorr multisig
End user can transfer in fund to this multisig account. The nodes can sign to transfer out the fund to an end user.
The transaction cna be observable from nodes.

### Uniswap

We build a simplified version of Uniswap to complete the user story. This part can be replaced by a more advanced version. We have the conceptual design of an advanced FEX swap but out of the scope.

## Demo story

Assuming the x-border system has been setup. 

Alice send (topup) 10 USD to Bob to receive (withdraw) 1000 JPY (assuming the exchange rate is 1:100)

Some notes:
- The exchange rate is determined by the simple uniswap algorithm.
- USD and JPY are two different layer1 blockchain to simulate two CDBC



## Demo system setup
#### Layer1
On the Goerli testnet, deploy two simulation ERC20. One represent USD, another present JPY. 

TEA Project has to handle both chains. Currently we can handle ETH only. This is one change we will need to make

#### Uniswap

To make the full demo story, we will need to setup a simple uniswap. We can use the existing Genesis Exchange logic. Because the uniswap is out of the scope, we only need such a placeholder. We need to explain that we have a full design but did not implement due to the scope.

Before the demo, we will put 10,000 USD, and 1M JPY as the liquidity pool. 

#### AWS Nitro nodes
We will deploy 3 A nodes. Represents three different counties.
We will deploy two B nodes, represent US and JP servers.
Alice will connect to the US B node, Bob to the JP B node.


## Stress test and scalability

The advantage of TEA over traditional blockchain is the scalability. We need to demonstrate this. Otherwise, the judge will think other competitor can do exactly (or even better) than ours.

We will need to run a stress test. See how many transaction we can handle. The layer1 is traditional blockchain, so we do not want it to bottleneck our performance, so we only stress test our internal transaction. 

## Development task

A Patricia tries state. We can update the root node once any change in the accounts.

A memory table stored: Timestamp, Txn hash, Patricia root after execution. This table can be output publicly.

If possible, make such state in a new native actor. Hope to be standalone from the existing token-state. Not sure if we should replace the token-state with this new Patricia

A simple UI to topup/withdraw/exchange

A stress test and output the benchmark

