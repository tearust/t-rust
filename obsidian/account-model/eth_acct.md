# The eth address and smart contract

## TEA_TOKEN_ADDRESS

this is a smart contract address in eth. It is used as standard ERC 20 and additional RA, AA mint etc. Our main TEA token logic stays here.

This contract is NOT payable. So far I did not think of any use case that other users pay ETH to this contract. We are not going to do ICO.

## TEA_L2_ADDRESS

This is the address inside TEA smart contract (layer1). It is used to reserve all TEA token that topup to layer2. 

The [[Global_state_maintainer_nodes]] controls the fund inside this address. When a user withdraw, the fund is transferred from this account to its layer1 address.

The features below are postponed to 2023!
```
This is a multisig account. It is controlled by the State Machine Validator group members. 

Because layer2 nodes cannot listen to every erc20 smart contracts. We have to have a list of smart contract addresses. In those smart contract, there is a TEA_L2_ADDRESS that used to store reserved this erc20 token. State machine nodes also listen to the fund_transfer_in_event, then emit layer two topup txn.

When user withdraw this token, the state machine nodes will multsign a txn to transfer fund to the receiptant. 

## Other project (new tapps or existing eth projects) smart contract address
Because TEA is just a erc20 compatible token. So we treat other erc20 token the same.


# Different Tokens can or cannot

| Feature | TEA | other erc20, such as USDT | tapp token from bonding curve |
| --- | ---- | ---- | ---- |
| Topup and withdraw between layer one and two | yes | yes |yes |
| Consume as payment inside a TApp | yes | yes | yes |
| Used to buy tapp token in bonding curve | yes | no | no |
| Used as purchase target others can use TEA to buy | no | no | yes |
| As the proof of ownership when share dividend of a tapp | yes | yes | yes |
| Tapp owner use to pay gas fee in tea project | yes | no | no |
| Miner's mining income | yes | no | no|
| Investors staking dividend share | no. Even tapp revenue in TEA, we still convert to tapp token in bonding curve | yes if app revenue in USDT | yes |
| ERC20 standard | yes | yes | yes |
| Consider security token | no | - | yes |

# Example combination of tokens if tapp use USDT as consumer payment:

TEA party issues tea_party_token as security (like stoke shares). Investors pay TEA to buy tea_party_token in bonding curve.
TEA party do not ask end user to pay tea_party_token to pay when posting message, instead, they use USDT to pay. 
Investors earn dividend in USDT based on how much tea_party_token they stake in bonding curve. 
Investors can trade tea_party_token as regular ERC 20 token outside of TEA Project after withdraw to layer1 (eth chain).
Investors can sell tea_party_token back to TEA using bonding curve.
TEA party app pays TEA token as gas fee. The gas fee is calculated by how much resources it used. 
Miners of TEA party earns TEA as mining income.


# Example combination of tokens if tapp use tapp_token as consumer payment:

TEA party issues tea_party_token as security (like stoke shares). Investors pay TEA to buy tea_party_token in bonding curve.

TEA party require consumer to pay tea_party_token to pay the usage (like posting messages). Consume can either using TEA to buy tea_party_token in the bonding curve, or use standard ERC 20 swap to get tea_party_token in layer one then topup to layer 2 before consuming.

Investors earn dividend in tea_party_token based on how much tea_party_token they stake in bonding curve. 

Investors can trade tea_party_token as regular ERC 20 token outside of TEA Project after withdraw to layer1 (eth chain).
Investors can sell tea_party_token back to TEA using bonding curve.
TEA party app owner need to sell tea_party_token to TEA, then use the TEA to pay gas fee. The gas fee is calculated by how much resources it used. 
Miners of TEA party earns TEA as mining income.

```

# Function

## Topup: transfer from layer one to layer two
This is not the original topup we used before. that topup is now called approve.

### input
token_id: the erc20 address (we used to use tappid) of the token smart contract. it is the unique id of every token type
amount: how much to transfer in
account: the eth address of the user
### output
Ok or err

### notes.
Alice can only transfer amount token_id from her own layer1 (eth) to her own layer2 account. She cannot transfer to other user's account.

## Withdraw: transfer from layer two to layer one.

### input

token_id: the erc20 address (we used to use tappid) of the token smart contract. it is the unique id of every token type
amount: how much to transfer in
account: the eth address of the user

### output

Ok or err

### notes.
Alice can only transfer amount token_id from her own layer two (tea project layer two) to her own layer one (eth) account. She cannot transfer to other user's account.

## Approve: approve tapp to consume a certain amount of any erc20 token without additional confirmation

this is similar to topup a deposit to a tapp. The tapp can consume up to this amount of fund. Depends on how the tapp desgiend. It can consume any erc20 token. Not limited to TEA, or this tapp native (security) token.

This operation was called "topup" before. Now we use approve to be consistent with eth erc20 standard name.

### input

delegator_tapp_id: the tapp id. Which app is allowed
token_id: which erc20 token that is approved to consume by tapp
amount: how much to allow. the unit is token_id above
account: who approve this allowance. Alice's address

### output

ok or err

### note

This function move the fund from ft_state.token_id.account to ft_allownce_state.token_id.account.
If fails when overdraft

## Revoke: revoke an existing approval. 

user decide to revoke a previous apprval. 
This operation was called "withddraw" before. Now we use approve to be consistent with eth erc21 standard name.

### input

delegate_tapp_id: the tapp id which app was previously allowed 
token_id: the tapp id. Which app is allowed
amount: how much to allow. the unit is token_id above, eg. usdt's address 0x55d398326f99059ff775485246999027b3197955
account: who previously approve this allowance. eg. Alice address

### output

ok or err

### note

This function move the fund from  ft_allownce_state.token_id.account to ft_state.token_id.account
faield when overdraft
Only the same user can operate.

## Deposit

Deposit can NOT be done by user. It is the app who can temporarily reserve a certain amount of token to process a multiple steps operation. Only tapp can deposit or refund. We do not allow user to refund because user may not know when the multiple steps operation will complete.
### input
token_id:
amount:
account:

### note
It moves the fund from ft_allowance_state.token_id.account to ft_deposit_state.token_id.account

## Refund
The oppsite of Deposit
### input
token_id:
amount:
account:

### note
It moves the fund from  ft_deposit_state.token_id.account to ft_allowance_state.token_id.account

refund can be only called by the tapp, not the user. When the tapp complete a multiple steps operation, it will refund unused fund back to the user.

# Workflow
Update Jul2022. The USDT paymetn postponed to 2023
```
## Example background of tea party that use USDT
Tea party has a te teaparty_token, which is a security token. It is not used to consume in tea party. Investors need to use TEA to buy teaparty_token
Tea party use usdt_token as utility. User pay usdt for posting messages.

The teaparty_token is only used for dividend. For example, the tea party earn 1000 usdt. it will be shared to all teaparty_token holders as dividend.

Similarly, our State machine can issue a state_machine_token as security. Investor use TEA token to buy state_machine_token. State machine earn TEA, and share TEA dividend to all state_machine_token holders.

Another example is Some_Game. It issues some_game_token both used as security token and utility token. This token is used to pay to play the game, also used as proof of dividend. 

## Topup from layer one to layer two

User Alice first will topup her own fund from layer one to layer two. The opposite operaiton is withdraw. 
When topup, he run commit a tx on ETH. transfer fund from his own eth address to TEA_L2_ADDRESS. 
Layer two state machine nodes (A nodes) listen to the fund_transfer_in_event then emit layer two topup transaction.
state machine handle this topup transaction to transfer layer two amount token from ft_state.token_id.hidden_system_account to her ft_state.token_id.alice_address.

Now, alice can check her metamask. She notice amount of TEA reduced in her eth. but she can also check layer two wallet and notice the amount increase in her layer two account.

at this time, the TEA_L2_ADDRESS in eth gain amount of TEA.

Note, it doesn't have to be TEA, it can be any ERC 20 token.

## Buy tea party token before using tea party

Alice want to be an investor of tea_party because she want to earn dividend from tea_party. She use bonding curve to pay tea_token and buy tea_party_token
After this operation, her ft_state.token_id_tea.alice_address reduced, but token_state.token_id_tea_party.alice_address increased.

This investoment action is optional. She doesn't need to do so if she is not interested in investing tea_party tapp. She can pay usdt to use tea_party app.

## Approve tea party

Before Alice can use tea party, she need to approve some fund first. Now she approve 10 usdt to tea_party app. After this operation. 10 usdt is transferred from ft_state.token_id_usdt.alice_acount to ft_allowance_state.token_id_tea_party.alice_usdt.

Now, tea party knows there is 10 usdt in alice allowance. Tea party app can consume up to 10 usdt if alice use the paid feature of tea party

## Consume tea party
Alice send 1 message, assume every message cost 0.1 usdt. The tea party will transfer 0.1 usdt from ft_allowance_state.token_id_tea_party.alice_usdt to ft_state.token_id_tea_party.hidden_consume_account. 

# If TEA party use tea_party_token to consume what will be different?
## use tea_party_token to consume in tea party

The previous example uses USDT as currency to pay posting message in tea party. What if the tea party developer decide to use tea_party_token to pay consuming. The gain is obviously, they can use boost the price of tea_party_token is it gets more and more popular. The cons are it adds initial barrier to the consumer, since they will need to exchange for tea_party_token before first time use.

## Provisioning steps

The major differences are in the application provisioning steps. Because the tappstore need to create a ERC 20 smart contract in eth layer1 when you create tapp.

Step1: In the tapp store UI, the tea party developer set all parameters. The most important different parameter is to set tea_party_token as its consuming currency. 

Step2: When user click create tapp. the tappstore will create a ERC 20 smart contract in layer one. The TEA_L2_ADDRESS address has the u128::MAX token as reserve. This is very improtant! Other address has zero balance at the beginning.

Step3: The tapp store continue to setup the layer2 state machine. This time, the hidden_system_account has u128::MAX amount of tea_party_token. This is also very improtant.

The rest would be pretty much the same as other tapps. When user buy tea_party_token using tea. the tea is stored in reserve, the tea_party_token is moved from the hidden_system_account is transferred to this user. So this user owns some tea_party_token in layer 2. He can withdraw to layer1 as the regular operation.

## TEA_L2_ADDRESS vs hidden_system_account

You probably have noticed that the hidden_system_account in layer two has the same funcitonity of TEA_L2_ADDRESS account in layer one. They are all used to store reserve token. Both of them are controlled by the state machine nodes.

```

# Data structure

``` json

ft_state:{
  token_id_tea_party:{//this is actaully app id of tea party. it is also an erc20 address of the smart contract
    alice_address:123,//unit is token_id_tea_party. Alice deposit 123 tea_party_token in tea_party app for future consume
    hidden_system_account:000,// should be removed

  },
  token_id_tea:{
    alice_address:294,//how much TEA that alice topup to layer two, and she has not deposit to any other tapp to consume yet.
    hidden_system_account:max-some_value,//this value in TEA , its init is u128::MAX. it is used to balance to layer one transfer
    tea_party_app_address://this is useless. because tea_party is an app, it is not a real user. this means nothing
  }
}

// used in approve
ft_allowance_state{
  token_id_tea_party:{//the id of the tapp. which tapp is allowed to use alice's fund
    alice_ustd: 926,//The key has the pattern like [user_address]_[token_id]. Alice allow tea_party to consume up to 926 ustd 
    // forexample: alice_account is 0x1234abcde, USDT address is 0x55d398326f99059ff775485246999027b3197955 the key is 0x1234abcde_0x55d398326f99059ff775485246999027b3197955.

  },
}

ft_deposit_state{
  token_id_tea_party:{
    alice_address:926,//unit is TEA, this balance is how much alice deposit to tea party and allow tea party to consume,
    // note, we allow any tea app to hold funds in TEA and tapp_own_token. 
  }
}
```

