Both [Home](Home.md) and [Discover](Discover.md) shows this page as content.
The different is that Home page only list [saved](saved.md) apps and tokens.

# Tab1: Apps

List of apps.
Columns:

* Name: App name. Click to go to new page [app_detail](app_detail.md)
* Tick: App tick. Click to go to new page [token_detail](../token_detail.md)
* Description: 
* Address: in the future, it is ERC20 address
* MarketCap:
* Current/Min/Max hosts: 
* Status: Active/Pending
* Action: [saved](saved.md) when in Discover, and unsave in Home.  Note, there is no host here. It moved to a new miner tab

# Tab2: Entities

List of Tokens
Columns:

* Token name: For CML it is CML#xxx; for app, it is APP#tick
* Address: In the future, it is ERC20 address
* Owner address: Click to show [user_page](../user_page.md)
* Description: can be empty for CML
* Total supply:
* Buy price:
* Sell price:
* Maketcap:
* Theta
* Actions: Buy/Sell

 > 
 > Q: Do we still need to show My total investment value in TEA?

No matter saved or not, the TAppStore is system app. its token is always listed at the top position.

Note: 

1. Investing CML and invest TApp are the same
1. Investing to AppStore is investing to the platform. We can probably call it Platform in the token (or Global as we called internally)
1. We do not provide the hosting feature to State Machine so far. 
