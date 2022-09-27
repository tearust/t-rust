# Goals
## Seperate TAppStore into several smaller TApps
- Leaderboard [[leaderboard_tapp]]
- Seat Auction Portal [[harberger_auction_tapp]] (oriingal tab Seats but add State Subscription Seat)

# TAppStore
After the change, the TAppStore will have a much simple UI.
It keeps
- Investments
- [[TApps]]
- Account
- Help
- Log

The Seats will move to [[harberger_auction_tapp]] . If the end user is not a maintainer and willing to be a maintainer, he probably will never need to go to [[harberger_auction_tapp]] .

The remaning features are the most relative features that a regular end user or investor need to use. The features are
- [[approve_allowance_to_tapp]]
- [[special_approval]]
- [[deposit_to_tapp]]
- [[check_tapp_allowrance]](the remaining allowance )
- [[launch_tapp]]
- check layer2 balance. this is tappstore balance after user topup from layer1
- check logs for all fund in/out events
- topup from layer1
- withdraw to layer1
- invest to CML, TApp, Global tokens

Once we moved to ETH, we do not need the features:
- Check layer1 Tea balance
- Exchange TEA to Coffee
- Check Coffee balance
- Trasnfer layer1 TEA/Coffee
Those featrues are done using ETH existing utilities, such as Uniswap, OpenSea, Metamask.

# Fund transfer between tapps rules
Statemachine will enforce rules when cross_move fund between apps
- If user did not login, no authkey, no operation is allowed
- With the new [[check_tapp_allowrance]] feature, the authkey can be simplified. Authkey only proof this user login to this TApp. TAppStore can check the allow/disallow table before execution any txns request from B actor. Becuase actually fund transfer or ownership transfer mostly happen in TAppStore. As long as we can control the TAppStore, we are most safe. For example, if this TApp has remaining allowrance, it can request the TAppStore to cross_move at most the remaining allowrance to this TApp's hidden consume account. TAppStore will also check before execute the cross_move.
- In TAppStore, user can explicitly use [[deposit_to_tapp]] to transfer fund to a TApp's deposit account. 

The operations that TApp can ask TAppStore
- [[TApp_fund_operations#Consume_from_tappstore | Consume from tappstore]]
- [[TApp_fund_operations#Deposit_from_tappstore| Deposit from tappstore]]
- [[TApp_fund_operations#Refund | Refund]]

# Fund transfer inside tapp rules

Statemachine will enforce rules when move fund inside a single app.
- If user not log in, no auth key, no operation is allowed
	- if user login, the app can charge (actually move) end user's fund from his deposit account to this app consume account. This is called [[TApp_fund_operations#in_app_consume| in app consume]]. This is different from[[TApp_fund_operations#Consume_from_tappstore| consume from tappstore]]. 
	
- (**In consideration, no decided yet**) Do not allow tapp to move consume account fund back to end user deposit account. This means if user paid something ther eis no way to get it back. 

