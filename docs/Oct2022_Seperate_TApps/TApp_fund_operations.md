# Consume_from_tappstore

The TApp can call consume when charge user payment.

consume is send to TAppStore actor.
params:

* actor. This is used to verify who is sending this req
* consume_amount
* authkey
* user_address

TAppStore check 

* authkey to make sure the user login valid
* this user allowrance > consume_amount
* txn coming from the actor of this tapp

Transfer route

* from ft_state -> tappstore_token_id -> user_address
* to ft_state -> tapp_token_id -> hidden_cosume_account

# Deposit_from_tappstore

User need to click deposit in TAppStore.

params:

* tapp_token_id. The targe tapp
* user_address
* deposit_amount

TAppStore check

* authkey, make sure the user matches
* this user has enough balance in tappstore

Transfer route

* from ft_state -> tappstore_token_id -> user_address
* to ft_deposit_state -> tapp_token_id -> user_address

# Refund

params:

* user_address
* refund_amount
  TAppStore check
* user address
* coming from the actor of this tapp

Transfer route

* from ft_deposit_state -> tapp_token_id -> user_address
* to ft_state -> tappstore_token_id -> user_address

# in_app_consume

No need TAppStore.
Transfer inside TApp. StateProvider should allow this operation directly.

Transfer route

* from ft_deposit_state -> tapp_token_id -> user_address
* to ft_state -> tappstore_token_id -> hidden_consume_account
