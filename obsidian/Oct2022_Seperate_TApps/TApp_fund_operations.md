# In_app_purchase
The TApp can call consume when charge user payment.

consume is send to TAppStore actor.
params:
- actor. This is used to verify who is sending this req
- consume_amount
- authkey
- user_address

TAppStore check 
- authkey to make sure the user login valid
- this user allowance > consume_amount
- txn coming from the actor of this tapp

Transfer route
-  from ft_state -> tappstore_token_id -> user_address  
-  to ft_state -> tapp_token_id -> hidden_cosume_account

# (Future feature)Deposit_from_tappstore
User need to click deposit in TAppStore.

params:
- tapp_token_id. The targe tapp
- user_address
- deposit_amount

TAppStore check
- authkey, make sure the user matches
- this user has enough balance in tappstore

Transfer route
- from ft_state -> tappstore_token_id -> user_address
- to ft_deposit_state -> tapp_token_id -> user_address

# (Future feature)Refund
params:
- user_address
- refund_amount
TAppStore check
- user address
- coming from the actor of this tapp

Transfer route
- from ft_deposit_state -> tapp_token_id -> user_address
- to ft_state -> tappstore_token_id -> user_address

# in_app_consume(from deposit)
No need TAppStore. This is not used in Harberger auction tapp. It may be used in other cases.
Different from #In_app_purchase becuase it transfer fund from deposit in this tapp, not from tappstore.
Transfer inside TApp. StateProvider should allow this operation directly.

Transfer route
- from ft_deposit_state -> tapp_token_id -> user_address
- to ft_state -> tappstore_token_id -> hidden_consume_account

# payment_from_tappstore
The TApp can call this txn when send one user payment to another user.
This is actove a move operation.

It happens inside the TApptore tokenid. The currency is TEA. 