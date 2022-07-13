# account-model pages

* [Intro to Account Model](account-model/README.md)
* [App Token](account-model/apptoken.md)
* [Eth Account](account-model/eth_acct.md)
* [Layer2 Bridge](account-model/layer2bridge.md)

# Update Jul 2022

We no longer plan payment in USDT feature in 2022.

Planned in 2022

* TEA Project layer1 smart contract in ETH
* Genesis block distribute all investors TEA token
* Genesis block reserve all layer2 token
* Vesting schedule in smart contract
* Topup withdraw TEA token between layer1 and layer2

Postpone to 2023

* Payment in  USDT (USDT actually means other ERC20 token)
* Bridge between USDT and TEA
* Topup and withdraw USDT
* AppToken support ERC20

# Layer1 smart contract design

## Standard ERC20 interface

## Topup withdraw between two layers

### Topup

Topup in layer1 is actually sending fund from sender to [eth_acct > TEA_L2_ADDRESS](eth_acct.md#tea-l2-address)\]. 

### Withdraw

Withdraw in layer1 is actually sending fund from [eth_acct > TEA_L2_ADDRESS](eth_acct.md#tea-l2-address) to receiver address.

### Layer2

The remaining workflow will be done in layer2. It mint new layer2 TEA in layer2 when toup, or burn Layer2 TEA when withdraw.

## CML tokens

CML is inside layer1 smart contract.

## RA

To reduce the layer1 cost, RA is intialized and verified in layer2. 
The punishment happens in layer1. 
Layer 1 also store the flag indicate that if a CML is "trusted (passed)" or "untrusted(not verified or verify failed)".

## Map between Hosting nodes to TApps

End user need to check this map to find out which IP address (hosting node) actually hosting any given TApp.

## List of bootstrap nodes

## List of State Maintainers public key

The smart contract logic will need this list to verify any txn that move fund in [eth_acct > TEA_L2_ADDRESS](eth_acct.md#tea-l2-address)
we do not need to public the IP address or peer ID, just public key.

## New block event

We use ETH mainchain new block event as trigger

## Machine

We need machine ID in layer1, so that the TEA Box manufacturer can register device with vendor signature. 

This may be chanced if we have a better way to do this in layer2. 

# Layer2 table and txn design

## Tables

````
pub type BalanceState = HashMap<Account, Balance>;

ft_states: HashMap<TokenId, BalanceState>,
token_states: HashMap<TokenId, BalanceState>,
````

ft_state store TEA only. 
token_states store token of every tapp. there is no TEA in this state.

the value is type of BalanceState. Very BalanceState has key of account, value of balance. (balance unit is TEA for ft_state, or unit is token for token_state)
There are a few special accounts that not belonging to any real human user.

* HIDDEN_CONSUME_ACCOUNT: 0xFFFF..., init value is 0, store the user consume TEA before a cron job to distribute dividends and utility bill.
* HIDDEN_SYSTEM_ACCOUNT: 0x0000...., init value is u128::MAX. it store the max of tea that used to transfer to user's account when user topup from layer1.
* HIDDEN_BONDING_RESERVE_ACCOUNT: this account address is the same as this tapp's layer 1 reserve account address. But it store the bonding curve reserved TEA in layer2. This address is only exists in TAppStore's ft_states. Other token_id != tapp_store_token_id, the value BalanceState won't have this address. 

For every tapp, it always has a HIDDEN_BONDING_RESERVE_ACCOUNT in `ft_states.get(tapp_store_token_id)`.

## Use cases

Let's assume a tapp name is App789 has a token_id = 0x789.
tappstore_token_id = 0x0,
Alice has address=0x123.
Alice has topupped 100T in Tappstore account
at this moment, assume the tapp App789 price is 10T/App789token.

### Buy token

Alice spend 20T in her tappstore layer2 account, in return she receive 2 App789token. 

Initial state:

`ft_states.get(tapp_store_token)` has table

|address|balance|notes|
|-------|-------|-----|
|123|100|Alice account in tappstore|
|789|0|init value 0, no token has been sold yet|
|0|doesn't matter number|this is tappstore reserve TEA as a standard tapp|

`ft_states.get(789)` has table 

|address|balance|notes|
|-------|-------|-----|
|123|0|Alice account in App789 layer2, Alice has no App789 token yet|

`token_state.get(789)` table

|addr|balance|notes|
|----|-------|-----|
|123|0|init value 0, App789 has not minted any token yet|

During buy operation
move alice address 123 20 T to address 789 inside `ft_balance under tapp_store`
calculate how much token 20T can buy, it is 2 App789 tokens
mint 2 App789token to alice address inside `ft_states.get(789)`

After buy operation
`ft_states.get(tapp_store_token)` has table

|address|balance|notes|
|-------|-------|-----|
|123|80|Alice account in tappstore|
|789|20|init value 0, no token has been sold yet|
|0|doesn't matter number|this is tappstore reserve TEA as a standard tapp|

`token_state.get(789)` table

|addr|balance|notes|
|----|-------|-----|
|123|2|Alice owns 2 App789 tokens|

### Sell token

TODO

## Auth

When move fund out of address 789 which owned by the App789, only tappstore actor can be authorized for this operation. This is a sell token OP

Buy token can be authornized by Alice, because it is only add fund to address 789 not moving out. As long as Alice authorize to move out fund from her address 123, it would be ok
