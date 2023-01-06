[[White_paper]]

TEA swap is a trusted network composed by specially designed hardware that runs fintech application such as automatic market maker (AMM) exchange or cross border settlement at low cost and high speed.

# Features

## Application features

### Continuous and automatic

AMM (Automatic market maker) doesn't require broker in the middle. It is software runs between nodes of the trusted network. It runs 24x7.

### Instant settlement

Unlike order book type exchange, the AMM is a peer to contract trade. The software calculate the exchange rate automatically and instantly make deal. 

### Low cost

Unlike blockchain based decentralized exchanges, TEA swap runs on a trusted network composed by specially designed hardware. The innovative consensus 
uses timestamp from GPS/Atomic-clock instead of burning energy (crypto mining). The cost is super low compare with any blockchain.

### CBDC technology agnostic

TEA Swap is not running on blockchain, it doesn't require the CBDC use any specific blockchain. Or not a blockchain at all. As long as the assets can be locked using cryptographic algorithm, it is tradable.

### Open liquidity provider

Anyone (Central banks, commercial banks, individual) can join the liquidity pool as liquidity provider to earn yield.

## Infrastructure features

### Hardware security

TEA Swap doesn't run on regular computers, it runs inside "enclaves" inside specially designed trusted hardware. This design makes it free from general attacks (virus etc.). 

### Trusted network

All nodes need to gain trust from constent **remote attestation** to keep serving in the network. This remote attestation is done by those trusted hardware. So the network is a kind of permissioned  network, unlike other permissioned network, the permission comes from hardware not human. Attemping to alter the software or hardware will cause remote attestation fail and large financial penalty.

### Time based consensus from atomic clocks

Unlikely blockchain-like consensus burn energy or require stakes to reach consensus between nodes, TEA infrastructure use real world timestamp from atomic clocks as the proof of events. This new kind of consensus runs green and reach consensus instantly between nodes all of the world. Because the specially design of the hardware, the cheapest way to get the atomic timestamp is receiving from GPS satallites from inside the enclave.

### Privacy protection

All code and data runs inside the enclave makes there is no way to peek the content from outside unless the code intent to. Application and system code is open sourced and under auditing.  Attempping to alter the execution code will result in failure of remote attestation. 

### Flexibility

This infrastructure is designed for general purpose computing not only for AMM exchange app. In the future, there should be all kinds of fintech applications available.

# Setup the network

Any BIS member can setup a TEA node ( the special designed computer) and join the network as validator. There is financial incentive of running validator. Technically any one can setup a node and join as long as policy permits. 

Those special designed hardware will require to connect to internet and visual to open sky for better GPS signal receiption. The location doesn't matter, it can be in member's country or delegated to data center in other country. 

During the prototype test, we simply use AWS Nitro (https://aws.amazon.com/ec2/nitro/). In the real network a wide selections from Amazon, Microsoft, or on-premise made hardware can be used.

As long as those hardware install the software and connect to the internet, it will work without any interaction. 

We will need at least 4 validators and 2 gateways to start the network. There is no upper limit of quantity of validators or gateways. In future production, the more nodes, the faster it runs. 

# API to existing CBDC

TEA Swap require the CBDC support multisignature ideally. This can make the TEA swap to reach highest security level. However, TEA swap can also support single signature at a relatively lower security protection. 

The k of n multisignature means an account with n private keys when created. As long as k of the n private keys sign a transaction, this transaction will be approved and executed.

# Liquidity provider

Not required to be the central banks, anyone (commercial banks, even individuals) can deposit their CBDC assets into the liquidity pool as a liquidity provider. They do this for incentive, they earn transaction fees but also take risk of impermenent loss. This is also called yield farming. They are not the brokers like those in the traditional order book trading system. All exchange transactions are handled by algorithm. Those liquidity providers do not process any order, they can adjust the liquidity pool parameters based on their investment strategy to gain most income as well as reduce impermenent loss.

# Automatic market maker algorithm

There are many AMM algorithms in the Defi world. They have different pros and cons. In the prototype, we will choose simple Uniswap V2 as an example. In the production we may choose other newer algorithms, such as Uniswap V3, Balancer or Bancor. 

# Demo and test cases

## Step1: Start the network

A group testers rent AWS Nitro machine from Amazon. Run a command to install the validator software (technically called state machines) in those machines. 

As long as the number of validators reaches 3, it will automatically make consensus. The 4th validator is a hot standby.

Another group of testers run at least 2 AWS Nitro machine. Run a command to install the gateway software (technically called hosts apps) in those machines.

There is no upper limit of how many gateway nodes. We suggest to run 2 test nodes  to demonstrate the decentralization and resilence.

## Step2: Liquidity providers deposit to fill in pool

A group of liquidity providers use their own computer browser to any of the gateway nodes. We use Metamask as example digital wallet. Those liquidity providers transfer at least two types of CBDC funds into the pool. Ideally we should have at least 3 different currencies in the pool to demonstrate multiple exchanges.

The testers can try out different investment strategy and compare their profit and loss.

## Step3: Foreign currency trade

A group of testers pre filled some funds of different CBDC currencies. They connect to gateway nodes from their own computer browser with Metamask installed. They can topup any CBDC and trade to any other CBDC fund. After exchange, withdraw to their own wallet.

## Step4: Liquidity provider withdraw from pool

After a series of exchange transactions, some liquidity providers can withdraw their fund from the pool. Verify the size of the pool affect the slippage. Check the profit and loss of those liquidity providers.

## Step5: Resilience

Try to shutdown any nodes, as long as the minimial requiements reached, the systemn should keep working without any issue.

## Step6: Compromise the software

Try to modify the software in any of the nodes, see if the remote attestation can kick the compromised node out of the network with a penalty.

## Step7: Compromise the hardware

Try to modify the hardware in any of the nodes, see if the remote attestation can kick the compromised node out of the network with a penalty.

## Step8: Stress test
User computer to generate a large quantity of transactions, see how the nodes process all the transanctions. Furthermore, try to simulate DDOS attack, test how the system is resilence for those kind of attack.

#  Business concepts

## What is lock account

Lock accounts are also called **Escrow** account. Before any CBDC fund can be used by the TEA Swap, it has to be locked in a TEA Swap controlled account. Only TEA Swap can use multisig to transfer the fund from this account.

### Lock account for every CBDC

After the TEA Swap system first start, there isn't any lock account for any CBDC, nor any Liquidity Pool for any trading pair. Before anyone can use the system, the first liquidity provider who generate a new liquidity pool will trigger the system to initialize lock account for any new CBDC. 

For any CBDC currency, only one lock account need to be initialized. For example, if Alice generates EUR <> SGD liquidity pool, the system will automatically generate lock accounts for EUR and SGD. Later on Bob generates EUR <> JPY liquidity pool, only JPY lock account will be genereated this time, because EUR has been created already.

## Initializing lock account

When system generate new lock account for a CBDC, a public key is created using the multisig cryptographic algorithm such as "k of n" Schnorr mulsig(https://en.wikipedia.org/wiki/Schnorr_signature). The public is the account address. In Schnorr case, there is no single private key to this public key, instead, there will be n private key pieces shared by n nodes. Every one of the n node only know one of the n pieces. The Schnorr mulsig algorithm allows k of n signature to approve the transaction from this public address.

If any CBDC doesn't support Schnorr mulsig,  Shamir algorithm (https://en.wikipedia.org/wiki/Shamir%27s_Secret_Sharing) can be used instead. The cons of Shamir is that we have to select one node to restore the private key from k of n pieces. This generate additional security risk.

Once the lock account is created, the public key (address) is disclosed to public. Whoever want to topup (deposit) fund to TEA Swap just need to transfer fund to this address. 

## Topup, Withdraw, Wrapped CBDC <> Real CBCD

TEA Swap cannot directly transfer fund from any CBDC account in real time. The user need to topup (deposit) fund to the lock account first. When system confirmed the lock account received deposit fund, it will mint wrapped CBDC token inside TEA Swap. This new minted wrapped token is only used inside TEA Swap, and will be burnt when user withdraw from the lock account.

For example, Alice send 100 SGD to the lock account in Singapore CBDC. The validator nodes are monitoring events from the transaction on Singapore CBDC. If more than 2/3 validator approve that Alice successfully transferred 100 SGD (topup), they will sign a transaction to mint 100 Wrapped SGD under Alice's account on TEA Swap. At this time, Alice can look up the 100 SGD balance in her TEA Swap account. Assuming Alice exchanges some of her SGD to 10 EUR. After a successful trade (let's skip the detail here) she will find 10 EUR (actually Wrapped EUR) in her TEA Swap account. If Alice withdraws 10 EUR, the validator will burn the 10 Wrapped EUR at the same time sign a transaction of sending 10 EUR from the locked account of EUR CBDC. Note, the 10 "real" or "unwrapped" EUR is actually someone else (Bob, or any liquidity provider) deposited to the lock account of EUR CBDC. Since Wrapped  token only mint when "real" CBDC locked (deposit/topup) and burn when "real" CBDC unlocked (withdraw). The wrapped and real CBDC is always 1:1 mapping. 

## Price feeding oracle

Depending which AMM algorithm to choose, most of them require price feeding oracle. It query the exchange rate from other exchange (e.g. centralized foreigh exchange) and feed into the AMM. 

TEA infrastructure is a trusted network, it has built-in oracle system which can be used for price feeding. 

## AMM Algorithm

There are many popular AMM algorithms in the Defi world. This is one of the most innovative area. In the prototype we use the most popular Uniswap V2 for demo purpose. For more detail about Uniswap please go to https://www.semanticscholar.org/paper/Uniswap-v2-Core-Adams-Zinsmeister/3bf68dddcd4e817e50539a1382da701defef04a0

# Infrastructure concepts

## Trusted computing technology

## Remote attestation network

## Time based consensus on distributed state machine (distributed ledger)

## Validator (state maintainer) and Gateway (hosts)

## WebAssembly runtime

## Resilience design

## Attack prevention

### Zero day attack

### Side channel attack

### DDoS attack

## Overall safty boundary



