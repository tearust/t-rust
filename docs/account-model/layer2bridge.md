# General idea behind layer two bridge

Make our TEA Project layer two a common layer two for both Substrate Layer1 and Ethereum (as layer one).

## Benefits

* Only miners need to run wallet based on Substrate layer1 with Polkadot extension.
  General users do not need. They can use Metamask to run TApps.
* Majority of mining logic and token reward logic still exists in Substrate layer one. minimize our work load
* We can still use Cumulus to run parachain if we want
* No need to external bridge.
* TApp can issue common ERC 20 token. TEA can be an ERC 20 as well.

## Workflow of using TEA Project layer2 as a bridge

Read the following spreadsheet. The steps of moving fund between Substrate Layer one to Ethereum smart contract (TEA).

https://docs.google.com/spreadsheets/d/1Tj_y_i8dCYb_enOON61IzGN2-fpu1EfCGIXrHV0H7Vw/edit?usp=sharing

## Tasks

### Create a TEA smart contract on eth

Because ETH doesn't support Schnorr musig, but smart contract can be used instead.
We will need to mantain a list of State Machine Nodes public key (in ETH). Whenever the L2 validators change, this list need to get updated. (very important!!!).

As long as majority of the members of this list sign a transaction, the smart contract will execute to transfer fund from this smart contract.

### Create HiddenEthAccount

This is similar to HiddenSystemAccount, but it is facing the ETH instead of our own layer one.

It also has init fund of u128::Max

### TApplication app token in ETH

Our Tapp can also create their own token on ETH chain.
But these token will not be in our Substrate layer one.
These token can be transfer (topup withdraw) between our layer2 and ETH smart contract

Because these tokens are ERC20. They can be trade using typical ERC20. The only different for these token smart contract is the special TappStore address. This address has init balance of MAX. It is controlled by our TAppStore in layer2.

### Using MetaMask to login

If we can allow user use MetaMask to login, for most users, they no longer need a Polkadot extension at all.

We can design the same logic as we did using Polkadot extension but using Metamask. 

## FAQ

### Who still need to use Polkadot extension?

Those miners who get mint TEA token will need to use Polkadot extension and our original wallet to topup their TEA from Substrate layer1 to layer2.

Others users can use Metamask only, because their TEA only exists between layer2 and ETH smart contract. Of course, we will need to support MetaMask login first.

### How to do TEA exchange?

The smart contract TEA (we can call it wrapper TEA or `TEA_eth`) is a ERC20 token. It can swap anywhere (CEX or DEX). As long as user exchanged wrapped TEA, they can just topup to layer2 and use them.

For most users, they probably never need to use our Polkadot based wallet if they are not a miner.

### Why do we still need Substrate layer1?

The main reason is that we issue (mint) original TEA token to the Substrate layer1.
Miners who recevied these TEA token will need to use the original wallet to topup to layer2. then they can either consume or transfer to ETH as wrapped TEA to exchange etc.
