# Goals
## Seperate TAppStore into several smaller TApps
- Leaderboard [[leaderboard_tapp]]
-  Seat Auction Portal [[harberger_auction_tapp]] (oriingal tab Seats but add State Subscription Seat)


## Make a clear boundary between TAppStore and Tapps
- Clear fund trasnfer routes 
- Each tapp has it's own deposit and consume accounts
- TApp will only charge fee from its own user deposit accounts
- TApp pay expense, tax and dividend
# TAppStore
After the change, the TAppStore will have a much simple UI.
It keeps
- Investments
- [[TApps]]
- Account
- Help
- Log

The Seats will move to [[harberger_auction_tapp]] . If the end user is not a maintainer and willing to be a maintainer, he probably will never need to go to [[harberger_auction_tapp]] ]. 

The remaning features are the most relative features that a regular end user or investor need to use. The features are
- [[deposit_to_tapp]]
- [[check_tapp_deposit_balance]]
- [[launch_tapp]]
- [[special_approval]]
- check layer2 balance. this is tappstore balance after user topup from layer1
- check logs for all fund in/out events
- topup from layer1
- withdraw to layer1

Once we moved to ETH, we do not need the features:
- Check layer1 Tea balance
- Exchange TEA to Coffee
- Check Coffee balance
- Trasnfer layer1 TEA/Coffee
Those featrues are done using ETH existing utilities, such as Uniswap, Metamask.

# Fund transfer between tapps rules
Statemachine will enforce rules when cross_move fund between apps
- If user did not login, no authkey, no operation is allowed
- if loggedin, authkey allow ready only, only read op code allowd
- if authkey allow move, it can only move from the login TApp token_id to other token_id. Do not allow to move fund from other token_id from the login tapp token_id. 

# Fund transfer inside tapp rules

Statemachine will enforce rules when move fund inside a single app.
- If user not log in, no auth key, no operation is allowed
- if user login, only allow read, the read op code is allowed
- if authkey allowed move, the app can charge (actually move) end user's fund from his deposit account to this app consume account.
- (**In consideration, no decided yet**) Do not allow tapp to move consume account fund back to end user deposit account. This means if user paid something ther eis no way to get it back. 

