# account-model pages

- [App Token](account-model/apptoken.md)

# Update Aug 2022
## Class relationship
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

# Double map of State

`State[Token_id][Address] = Balance`

The combination of first and second keys can make different meaning.

Example:
`state[TeaParty_app_token_id][alice_erc20_address]` returns how many Alice owns TeaPartyToken (the bonding curve token).
`state[GlobalToken_id][alice_erc_20_address]` returns how many GlobalToken Alice owns.
`state[TEA_ERC20_address][alice_erc_20_address]` returns how many TEA stays in Alice's layer2 wallet (we call it TAppStore wallet).


## Special account/token_id
> Please remove consume account.  We use this tapp tokenid address as its consume account address
> 
### Deposit account
pub const HIDDEN_DEPOSIT_ACCOUNT: Account = [0u8;32];
Used as a TokenId not as Account. 
`State[HIDDEN_DEPOSIT_ACCOUNT][Alice_Address]` is the balance of Alice's deposit TEA. 

### Bonding curve account
pub const HIDDEN_BONDING_CURVE_ACCOUNT: Account = [1u8; 32];
This is used as a TokenId, not an Account.
This is the bonding curve reserve account.
`state[GlobalToken_address][HIDDEN_BONDING_CURVE_ACCOUNT]` is the balance of TEA that reserved for GlobalToken bonding curve.

When "consume" or "buy" in bonding curve happens, the TEA token used to buy TAppToken will be reserved here. When user sell TAppToken, the TEA token in this acocunt will be paid out to this user.

### Seat collection pool
pub const SEAT_COLLECTION_POOL: Account = [3u8; 32];
Memory tax is paid to this collection pool before deduct maintainer tax and then shared to all state maintainer as income,

### Seat distribution pool
pub const SEAT_DISTRIBUTION_POOL: Account = [4u8; 32];

maintainer's income tax is stored in this pool before pay off public service then share to all Global Token holders.

### DAO reserved account
pub const DAO_RESERVED_ACCOUNT: Account = [254u8; 32] as Account;

This reservation is topup from a reserved Genesis Block reservation layer1 account when TEA Project starts. The fund in this account is used to support public service at early stage. Early stage means the begining peirod that not enough TApp to generate enough tax to pay off public service. In order to make public service continue, we use this reserved TEA token to pay.

## Typical operation
### Deposit and refund
Case 1: Alice deposit 100 TEA token 
```
tea_state.ft_state[TEA_ERC20_address][Alice_address] -= 100
tea_state.ft_state[HIDDEN_DEPOSIT_ACCOUNT][Alice_address] += 100
```
Refund is reverse operation
Note: Deposit is only for TEA token. You cannot deposit USDT

### Buy GlobalToken
Alice spend 100T to buy GlobalToken

GlobalToken_address is a TokenId , a random address [u8;32] generated when initialize TAppStore.

```
tea_state.ft_state[TEA_ERC20_address][Alice_address] -= 100
tea_state.ft_state[GlobalToken_address][HIDDEN_BONDING_CURVE_ACCOUNT] += 100
let mint_new_token_amount = calculate_token_amount(100)
tea_state.ft_state[GlobalToken_address][Alice_address] += mint_new_token_amount

```
Sell GlobalToken is reverse operation

> Note: The bonding_state.token_reserved_state should be removed. it is useless
> 
### Consume in TEA Party App
TeaParty_address is tokenid for TeaParty App. It is random generated u8;32 address when TeaParty init

Case: Alice spend 100TEA in TeaParty app
```
tea_state.ft_state[TEA_ERC20_address][Alice_address] -= 100
tea_state.ft_state[TEA_ERC20_address][TeaParty_address] += 100

```
In next cron job, this 100 TEA will be used to "consume" to buy TeaParty token and shared to all TeaParty token holders.

### Topup USDT
Case: Alice topup 10 USDT to TAppStore

```
tea_state.ft_state[USDT_ERC20_address][Alice_address] += 10

```

### Consume USDT in TEA Party
If TeaParty allow use to pay in USDT.

```
tea_state.ft_state[USDT_ERC20_address][Alice_address] -= 10
tea_state.ft_state[USDT_ERC20_address][TeaParty_address] += 10
```
But we cannot run "consume" those USDT to buy Teaparty token. We donot accept buying TAppToken using non-TEA token.

# Changes
In this case, we can make tea_state directly a double map because tea_state.ft_deposit_state is useless. We can make tea_state: Map<Address, <Address, Balance>>.

GeneralState.bonding_state is useless too. We can remove it and its BondingState type

After modification:
GeneralState has
- tea_state:BalanceState
- key_state:KeystoreState

We can also remove the UtilityState type

