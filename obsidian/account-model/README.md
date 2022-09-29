# account-model pages

- [App Token](apptoken.md)

# Existing data structure Aug 2022
State machine has General State object
General State has
- tea_state: UtilityState
- bonding_state: BondingState
- key_state: KeystoreState

UtilityState has
- ft_state: BalanceState
- ft_deposit_state: BalanceState

BalanceState is Map<token_id, Balance>

Balance is Map<Account, Balance>

token_id is TokenId is [u8;32]. This is the ERC20 smart contract address in ETH. For example, the TEA in Goerli testnet is 0x1ec67D41a35CA11f07Fc3a6ed03B92A483d1AA61

# Proposed Double map of State

`State[Token_address][User_address] => Balance`

Token_address is the [u8;32] address that represents the TApp. It could be the TEA_ERC20_Smart_Contract deployed to ETH. It could be the random generated TeaParty App token_id. 

User_address is a [u8;32] address, mostly represent a user address. But some spcial predefined address has special use case. They are listed in next section.

The Token_address can be used in the second key, such as `state[TEA_ERC20_address][Teaparty_token_address]` means Tea Party app consume account balance.


The User_address can be used in the first key when used as deposit. Such as
`state[Alice_addres][Teaparty_token_address]` means Alice has deposit to TeaParty App.

## Predefined Special account
> No longer need consume account.  We use this tapp address as its consume account address


### Deposit account

### TOTAL_SUPPLY_RESERVE
pub const TOTAL_SUPPLY_RESERVE:Account = [255u8;32];
The total supply of a TAppToken. Its initial value is u32::MAX. When mining new TAppToken, it is actaully moving reserved TAppToken out from TOTAL_SUPPLY_RESERVE, when burn, moving in to this acocunt.
`state[GlobalToken_address][TOTAL_SUPPLY_RESERVE]` Use u32::MAX minus this balance you will get the totaly supply of minted GlobalToken
`state[TEA_ERC20_Address][TOTAL_SUPPLY_RESERVE]` Use u32::MAX minus this value you can get the totaly topped up TEA token to layer 2.

The token of this balance is defined by the first key. GlobalToken_address or TEA_ERC20_Address

### Bonding curve account
pub const HIDDEN_BONDING_CURVE_ACCOUNT: Account = [1u8; 32];
This is used as a TokenId, not an Account.
This is the bonding curve reserve account.
`state[GlobalToken_address][HIDDEN_BONDING_CURVE_ACCOUNT]` is the balance of TEA that reserved for GlobalToken bonding curve.

When "consume" or "buy" in bonding curve happens, the TEA token used to buy TAppToken will be reserved here. When user sell TAppToken, the TEA token in this acocunt will be paid out to this user.

This address only apply to a TAppToken, the first key must be a TAppToken. TApp
Token support Bonding Curve. You cannot use TEA_ERC20_Address or USDT_ERC20_Address as the first key.

### Seat collection pool
pub const SEAT_COLLECTION_POOL: Account = [3u8; 32];
Memory tax is paid to this collection pool before deduct maintainer tax and then shared to all state maintainer as income. 

`state[TEA_ERC20_Address][SEAT_COLLECTION_POOL]` 

This address only apply to TEA_ERC20_Address, the first key must be TEA_ERC20_Address. 
### Seat distribution pool
pub const SEAT_DISTRIBUTION_POOL: Account = [4u8; 32];

maintainer's income tax is stored in this pool before pay off public service then share to all Global Token holders.

`state[TEA_ERC20_Address][SEAT_DISTRIBUTION_POOL]` 
This address only apply to TEA_ERC20_Address, the first key must be TEA_ERC20_Address. 

### DAO reserved account
pub const DAO_RESERVED_ACCOUNT: Account = [254u8; 32] as Account;

This reservation is topup from a reserved Genesis Block reservation layer1 account when TEA Project starts. The fund in this account is used to support public service at early stage. Early stage means the begining peirod that not enough TApp to generate enough tax to pay off public service. In order to make public service continue, we use this reserved TEA token to pay.
`state[TEA_ERC20_Address][DAO_RESERVED_ACCOUNT]` 

This address only apply to TEA_ERC20_Address, the first key must be TEA_ERC20_Address. 
## Combination of double map and meaning

### Any user's TEA layer2 balance (Any user's topped up TEA)
`state[TEA_ERC20_address][alice_erc_20_address]` returns how many TEA stays in Alice's layer2 wallet (we call it TAppStore wallet). Alice can see this in the Account page TappStore wallet balance on UI.

### Total layer2 TEA token for all users (total topped up TEA)
`state[TEA_ERC20_address][TOTAL_SUPPLY_RESERVE]` returns u32::MAX minus the total layer2 (tappstore) TEA. When any user topup, this value decrease. When withdraw, increase. We need this Total Supply because we cannot iterate all address and sum up.

u32::MAX minus this number should be always exactly the same as  layer1 Lock.sol smart contract total lock. 

### Any user's TappStore token balance
`state[TeaParty_app_token_address][alice_erc20_address]` returns how many Alice owns TeaPartyToken (the bonding curve token).

### Any user's GlobalToken balance

`state[GlobalToken_address][alice_erc_20_address]` returns how many GlobalToken Alice owns.

### GlobalToken total supply
`state[GlobalToken_address][TOTAL_SUPPLY_RESERVE]` returns u32::MAX minus the total supply of GlobalToken. Everytime new GlobalToken is minted, this value need to increase. When burn, decrease. Since we cannot iterate all acocunt to get the total supply. This account record the total supply.

### TeaParty income (in TEA) before "consume" action to bonding curve
When user spend TEA token in Tea Party App, the income will be stored in the Consume Account of TeaParty temporarily. In a corn job, it will be used to "consume" in bonding curve. That means buying TeaParty token then share to all token holder. 

`state[TEA_ERC20_address][TeaParty_app_token_address]` returns the temporary income balance before "consume".

If TeaParty allows user to pay USDT instead of TEA, the double map will look like `state[USDT_ERC20_address][TeaParty_app_token_address]`, but we cannot use USDT to buy TeaParty Token. This money will be owned by the App owner. Probably can be later uniswap to TEA. This is not in the scope of this document.

### DAO Reserved TEA as subsidy of Public Service
`state[TEA_ERC20_address][DAO_RESERVED_ACCOUNT]`. 

### GlobalToken bonding reserved account
`state[GlobalToken_address][HIDDEN_BONDING_CURVE_ACCOUNT]`. When new GlobalToken is mint (either user buy or app consume), the purchase TEA will be stored here. When GlobalToken is burnt (either user sell or app expense), TEA token will be withdrawn from this account.

### Alice deposit TEA to TeaParty in deposit account
``state[Alice_addres][Teaparty_token_address]`
This can only be TEA token. Other token cannot be deposit. TeaPartyApp can use this deposit when Alice is consume in TeaParty app. After user action, remaining part will be refund back to Alice layer2 TEA account `state[TEA_ERC20_address][Alice_address]`.

The fund in the deposit account is tempoerarily owned by the TeaparyApp. But TeaParty cannot consume this fund. This is the only difference between deposit account and consume account. Consume account `state[TEA_ERC20_address][TeaParty_address]` is owned by the TeaParty as income. In the next cron job it will be "consume" to TeaParty token. 

Note, TEA Party will put all deposit into this single account. So there is no way to distinguish Alice's deposit with Bob's deposit. It is Tea Party 's reponsibility to track  Alice deposit amount and refund correct amount later.

## Meaningless combination examples, do not use
The following combination were wrong. They are meaningless, and do not use

`state[TEA_ERC20_address][HIDDEN_BONDING_CURVE_ACCOUNT]`  TEA is not a bonding curve. 

`state[USDT_ERC20_address][HIDDEN_BONDING_CURVE_ACCOUNT]`  USDT is not a bonding curve. 

`state[GlobalToken_address][DAO_RESERVED_ACCOUNT]` DAO Reserved only apply to TEA token. There is no reserved Global Token, nor any other (USDT for example)



## Typical operation
### Deposit and refund
Case 1: Alice deposit 100 TEA token  to Tea Party
```
state[TEA_ERC20_address][Alice_address] move out 100
state[Alice_addres][Teaparty_token_address] move in 100
```
Refund is reverse operation
Note: Deposit is only for TEA token. You cannot deposit USDT

### Buy GlobalToken
Alice spend 100T to buy GlobalToken

GlobalToken_address is a TokenId of TAppStore app ,it is a random address [u8;32] generated when initialize TAppStore.

```
state[TEA_ERC20_address][Alice_address] move out 100
state[GlobalToken_address][HIDDEN_BONDING_CURVE_ACCOUNT] move in 100
let mint_new_token_amount = calculate_token_amount(100)
state[GlobalToken_address][Alice_address] move in mint_new_token_amount
state[GlobalToken_address][TOTAL_SUPPLY_RESERVE] move out mint_new_token_amount

```
Note in the code example above, TEA can only be move in or out. Because TEA cannot be mint or burnt. But GlobalToken is bonding curve token, it can be mint or burnt.

Sell GlobalToken is reverse operation

### Consume TEA in TEA Party App
TeaParty_address is tokenid for TeaParty App. It is random generated u8;32 address when TeaParty init

Case: Alice spend 100TEA in TeaParty app
```
state[TEA_ERC20_address][Alice_address] move out 100
state[TEA_ERC20_address][TeaParty_address] move in 100

```

In next cron job, this 100 TEA will be used to "consume" to buy TeaParty token and shared to all TeaParty token holders. That would be the same as Buy TeaParty Token. Please reference the Buy GlobalToken above.

```
let all_tea_in_teaparty_consume_account = state[TEA_ERC20_address][TeaParty_address];
let new_mint_teaparty_token = buy_teaparty_token(all_tea_in_teaparty_consume_account);
Share new_mint_teaparty_token to all TeapartyToken holders as dividend.
```

### Topup USDT
Case: Alice topup 10 USDT to TAppStore

```
layer1 USDT_ERC20.sol(Deployed to USDT_ERC20_Address) transfer 10 from Alice_address to Lock.sol(Deployed to Lock_ERC20_Address)

state[USDT_ERC20_address][Alice_address] move in 10
state[USDT_ERC20_address][TOTAL_SUPPLY_RESERVE] move out 10
```
This operation cause Alice's USDT_ERC20 smart contract reduce 10 USDT, while Lock.sol smart contract gain 10USDT. These all happened on layer1.

If you replace USDT to TEA, it works the same way when topup TEA.

### Consume USDT in TEA Party
If TeaParty allow use to pay in USDT. This is a new feature we have not decided yet

```
state[USDT_ERC20_address][Alice_address] move out 10
state[USDT_ERC20_address][TeaParty_address] move in 10
```
But we cannot run "consume" those USDT to buy Teaparty token. We donot accept buying TAppToken using non-TEA token.

### Consume TeaParty Token in Tea Party App
If TeaParty allow use to pay in TeaParty Token. This is a new feature we have not decided yet

Alice pay 1 TeaParty token to use Teaparty app.

```
state[TeaParty_Token_address][Alice_address] move out 1
state[TeaParty_Token_address][TeaParty_address] move in 1
```

After a while, when the Consume cron job starts, it will
```
let all_tea_party_token_in_consume_account = state[TeaParty_Token_address][TeaParty_address];

Share all_tea_party_token_in_consume_account to all TeapatyTokenHolders as dividend.

Set state[TeaParty_Token_address][TeaParty_address] to 0
```

Note: We have to allow a limited token type allowed for Tea party app to allow users to pay. We cannot allow them to pay any ERC20 token. So name a few
- TEA
- USDT/USDC
- DAI
- TeaParty Token
As long as it is a limited list, we can always iterate all token addresses in the consume cron job.

# The Neutral Context Balance for every token_address

Because we only move in and move out token between accounts **within** the same TAppToken_address. (the first key). The balance of this TAppToken should always be zero. New new token is mint or burn (just move in and out between accounts of TOTAL_SUPPLY_RESERVE).


# Development changes
In this case, we can make tea_state directly a double map because tea_state.ft_deposit_state is useless. We can make tea_state: Map<Address, <Address, Balance>>.

GeneralState.bonding_state is useless too. We can remove it and its BondingState type

After modification:
GeneralState has
- tea_state:BalanceState
- key_state:KeystoreState

We can also remove the UtilityState type

