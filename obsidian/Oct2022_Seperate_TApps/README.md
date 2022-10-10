# Goals
## Seperate TAppStore into several smaller TApps
- Leaderboard [[leaderboard_tapp]]
- Seat Auction Portal [[harberger_auction_tapp]] (oriingal tab Seats but add State Subscription Seat)
- Miner's portal [[miner_portal]]
- Seeds market [[seeds_market]]
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
- [[check_tapp_allowance]](the remaining allowance )
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
- With the new [[check_tapp_allowance]] feature, the authkey can be simplified. Authkey only proof this user login to this TApp. TAppStore can check the allow/disallow table before execution any txns request from B actor. Becuase actually fund transfer or ownership transfer mostly happen in TAppStore. As long as we can control the TAppStore, we are most safe. For example, if this TApp has remaining allowance, it can request the TAppStore to cross_move at most the remaining allowance to this TApp's hidden consume account. TAppStore will also check before execute the cross_move.

The operations that TApp can ask TAppStore
- [[TApp_fund_operations#In_app_purchase| in app purchase]] .e.g. maintainer buy seat, use this money to pay the seller the seat price.
- [[TApp_fund_operations#Deposit_from_tappstore| Deposit from tappstore]] e.g. maintainer buy seat and pay seat deposit form allowance to deposit account
- [[TApp_fund_operations#Refund | Refund]]. e.g. maintainer giveup or sold seat, the remaining deposit shoudl go back to his tappstore balance.

# Fund transfer inside tapp rules

Statemachine will enforce rules when move fund inside a single app.
- If user not log in, no auth key, no operation is allowed
	- if user login, the app can charge (actually move) end user's fund from his deposit account to this app consume account. This is called [[TApp_fund_operations#in_app_consume| in app consume]]. This is different from[[TApp_fund_operations#Consume_from_tappstore| consume from tappstore]]. 
	
- (**In consideration, no decided yet**) Do not allow tapp to move consume account fund back to end user deposit account. This means if user paid something ther eis no way to get it back. 

# Example workflow for Seat auction
## [[login_harberger]]
## [[list_seats]]
## [[buy_seat]]
## [[giveup_seat]]
## [[update_price]]

When buying first batch seat from sudo, the seller account is TAppStore hidden_consume_account.

# FAQs
## Does TAppStore pay memory tax or txn exec fee?
No, if it pay, it will just pay to itself. so it would be nonsense.

# Why would (mostly) very hosting nodes would like to host TAppStore? Benefits?
TAppStore is the default applications entry. By default, user will click tapp link to launch apps in THE SAME HOST. So the host will get more traffic and gas fee if hosting tappstore.

Tappstore itself is profitable. User need to pay to use features in tappstore. hosting earn gas fee.

# What the wallet.teaproject.org will look like at production?
It will show a list of all registered hosting nodes that hosts TAppStore. End user can click to any of them to launch TAppStore. From there, they will access to any other TApp.

# Will there be a https://store.teaproject.org?
yes, it will be the same as wallet.teaproject.org, or maybe replace it in the future.

# I am a hosting CML owner, do I have to register in TAppStore?
You do not have to. You regsiter because you can be listed in the default store.teaproject.org. As long as you can promot yourself to end user and gain traffic, you do not need to.

# Can I host other TApps, but not hosting TAppStore?
Yes, you can. You just missed the entrance to other apps for your end user. 

# Is there any exchange in TEA Project?
Not yet, but plan to have an Uniswap like layer2 DEX. At this moment, use any exchange in ERC20 competible exchange will work. 

# Will we have a CML seed auction app?
It is planed in Epoch12. In Epoch11, buying a CML seed is still a manual process. Buying an existing CML can be done using OpenSea.

# How can I release my vested TEA Token
If you login using your address which owns any vested TEA, you will see a "TEA Vesting" tab in TAppStore. You can check and release there.

# Why does my Global token account balance shrinks?
Your token account holds Bonding Curve token which may increase or descrease based on the token entity operation. When the entity make profits, every one who hold this token will see the balance increase. On the other hands, when the entity is losing money, every one who hold this token will see the token balance decrease. 

