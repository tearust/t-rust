Seat auction tapp is mostly the original seat tab of tappstore, but we only have the auction logic and UI here. The cron job of paying tax, share dividends logics are still remaining in TAppStore.

Seat auction tapp is not only used in maintainer seat, but also in State Subscription Seat auction. It is general purpose auction TApp that compatible with Harberger Tax rule.

If end user want to buy / sell seats, they will need to come to this tapp. Otherwise, they would never need to come to this TApp.

This TApp is not free. All action (query and mutation) is paid.

# Spcial approval

# Login
TApp login is simplified, without the auth that required by [[special_approval]].

# Deposit
End user will need to use TAppStore TApps tab , the  [[approve_allowance_to_tapp]]  feature to add deposit. Maintainer need to pay deposit before he can pay anything in the maintainer tapp. 

For easier first-time-user ramp up, we are considering the [[special_approval#time_based_approval|time based approval]]

# Refund
User has [[refund]] feature in the [[#Account component]]. User can reduce or refund in full. Refund is sending this user deposit in this tapp (seat_auction_tapp) deposit back to the same user's TAppStore account.

Refund can only be done within the TApp, not TAppStore. 

> Question, what if the TApp has some bad behaivor logic that do not allow end user to refund?


# Account component
This account component can be show at the header.
It shows the current maintainer tapp deposit balance of this user.
A [[harberger_auction_tapp#Refund|refund]]refund button to refund the existing deposit back to tappstore. User will be promopt to input amount of TEA to refund. But [[approve_allowance_to_tapp|deposit]] need to be done inside tappstore.

# Auto deposit (future feature)
This is a feature plan to be done in epoch12. User can setup a rule, when deposit is low, it auto transfer from tappstore. This operation is [[special_approval]] inside TAppStore not in maintainer app.

# Seat table
No change from existing Seat table

# Quries

User will need to [[special_approval]] this TApp to query user's data and show in public. Seat data is public viewed by anyone. TAppStore will need such appoval from the end user to protect their privacy. 

## Check deposit
Use the statemachine query ft_deposit_state/maintainer_token_id/end_user_address to get the TEA balance

We probably no longer need to store the value in SQL

## Check seat list, price, status, price change etc.
Existing SQL 

# Mutations
## Buy or sell seat
The same logic but move to Seat Auction TApp.

# Cron job

All cron job (paying tax, share dividend to tappstore ) are done in TAppStore.
