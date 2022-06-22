# Tables
```
pub type BalanceState = HashMap<Account, Balance>;

ft_states: HashMap<TokenId, BalanceState>,
token_states: HashMap<TokenId, BalanceState>,
```

ft_state store TEA only. 
token_states store token of every tapp. there is no TEA in this state.

the value is type of BalanceState. Very BalanceState has key of account, value of balance. (balance unit is TEA for ft_state, or unit is token for token_state)
There are a few special accounts that not belonging to any real human user.
- HIDDEN_CONSUME_ACCOUNT: 0xFFFF..., init value is 0, store the user consume TEA before a cron job to distribute dividends and utility bill.
- HIDDEN_SYSTEM_ACCOUNT: 0x0000...., init value is u128::MAX. it store the max of tea that used to transfer to user's account when user topup from layer1.
- HIDDEN_BONDING_RESERVE_ACCOUNT: this account address is the same as this tapp's layer 1 reserve account address. But it store the bonding curve reserved TEA in layer2. This address is only exists in TAppStore's ft_states. Other token_id != tapp_store_token_id, the value BalanceState won't have this address. 

For every tapp, it always has a HIDDEN_BONDING_RESERVE_ACCOUNT in `ft_states.get(tapp_store_token_id)`.

# Use cases
Let's assume a tapp name is App789 has a token_id = 0x789.
tappstore_token_id = 0x0,
Alice has address=0x123.
Alice has topupped 100T in Tappstore account
at this moment, assume the tapp App789 price is 10T/App789token.

## Buy token
Alice spend 20T in her tappstore layer2 account, in return she receive 2 App789token. 

Initial state:

`ft_states.get(tapp_store_token)` has table
| address | balance | notes |
| -------- |------- |------|
| 123 | 100 | Alice account in tappstore |
| 789 | 0 | init value 0, no token has been sold yet |
| 0 | doesn't matter number | this is tappstore reserve TEA as a standard tapp |

`ft_states.get(789)` has table 
| address | balance | notes |
| -------- |------- |------|
| 123 | 0 | Alice account in App789 layer2, Alice has no App789 token yet |

`token_state.get(789)` table

| addr | balance | notes |
| -------- |------- |------|
| 123 | 0 | init value 0, App789 has not minted any token yet |

During buy operation
move alice address 123 20 T to address 789 inside `ft_balance under tapp_store`
calculate how much token 20T can buy, it is 2 App789 tokens
mint 2 App789token to alice address inside `ft_states.get(789)`

After buy operation
`ft_states.get(tapp_store_token)` has table
| address | balance | notes |
| -------- |------- |------|
| 123 | 80 | Alice account in tappstore |
| 789 | 20| init value 0, no token has been sold yet |
| 0 | doesn't matter number | this is tappstore reserve TEA as a standard tapp |


`token_state.get(789)` table

| addr | balance | notes |
| -------- |------- |------|
| 123 | 2  | Alice owns 2 App789 tokens |

## Sell token
TODO

# Auth
When move fund out of address 789 which owned by the App789, only tappstore actor can be authorized for this operation. This is a sell token OP


Buy token can be authornized by Alice, because it is only add fund to address 789 not moving out. As long as Alice authorize to move out fund from her address 123, it would be ok


