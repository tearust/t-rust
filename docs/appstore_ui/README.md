## appstore_ui pages

* [Account](appstore_ui/Account.md)
* [App Detail](appstore_ui/app_detail.md)
* [Asset](appstore_ui/Asset.md)
* [Discover](appstore_ui/Discover.md)
* [Home](appstore_ui/Home.md)
* [Inbox](appstore_ui/Inbox.md)
* [Leaderboard](appstore_ui/Leader_board.md)
* [Log](appstore_ui/Log.md)
* [Marketplace](appstore_ui/Marketplace.md)
* [Mining](appstore_ui/Mining.md)
* [Saved](appstore_ui/saved.md)
* [User Pull Down](appstore_ui/User_pull_down.md)
* [Welcome](appstore_ui/Welcome.md)

# Major changes

1. AppStore replace wallet
1. AppStore has two modes. One fo regular non-miners. Another for miners. Only miners will see the complex mining related UI. Regular users will only see the tapp list, staking investment and regular token operation (transfer etc.)
1. We determine the current login user a miner or a regular user by search weather he owns any CML. For those own zero CML will be considered regular user.
1. User can invest any entities, ncluding CMLs, APPs, Global (Platform token). They are all called Tokens in the UI now. 
1. Headline shows account summary only. including: Name, Address, TEA Balance (transferrable TEA only). Others goes to the Account page

# Nav bar

Logo

* [Home](Home.md) // also includes [Account](Account.md) (detailed account information including TEA token balance) and then investments (tokens), and then user saved apps. 
* TApps // TApps to use. This is what most consumers will expect to see when we advertise that this is the TAppstore.
* Invest// All available TApp tokens, CML hosting tokens, and CML (global / state machine) tokens. Users come to this tab to invest.
* [Mining](Mining.md)// Only seen by miners. All mining related content in this page
* <s>
[Leader_board](Leader_board.md)</s>  // Only seen by miners. The same as current leader board. **Suggesting to delete this, see below**.
* [Inbox](Inbox.md)//Click go to TEA Party [app_detail](app_detail.md) page
* Help // Welcome, help, about etc.
* [User_pull_down](User_pull_down.md) //No change from existing version. login etc.

Some comments on this new structure:

First we want a usable appstore. We can always look at Apple's or Google's Play store for inspiration, but we want to move away from them seeing a "wall of text" as is currently the case:

![\_text-wall](https://user-images.githubusercontent.com/86096370/172469183-a9b7daaa-feac-47c5-be24-74c6f7478d1d.png)

So we have lots of changes for the "home" tab:

## 1. Replace [Welcome](Welcome.md) with [Home](Home.md) page

This new Home page tab also includes the [Account](Account.md) details integrated, similar to our existing **My Assets** page. It should contain all the important details about their user details including what they own. The flow of the container would be like this:

* Detailed account information including TEA token balance) and then investments (tokens), and then user's saved apps. It could actually look similar to the existing **My assets** page with the lower tabs edited like so:

<img width="1141" alt="Screen Shot 2022-06-07 at 1 01 29 PM" src="https://user-images.githubusercontent.com/86096370/172471857-0ab19c96-1eeb-432b-ac47-5d307010a237.png">

## Tabs 2 & 3 - TApps and Invest

The **TApps tab** is what most people think of when they envision an App Store. Can include a promo section like traditional app stores, categories, articles detailing "best decentralized _ apps". You can look at any app store - Apple or Google Play - for an example, they've designed their app stores with conversions in mind (getting users to explore and download apps). Main point is we want the consumers to have a place to go to find apps to use, very different from next tab which is investment oriented.

One question about the **TApps tab** - does the list of TApps inherit the mining display (host/unhost max / min nodes etc.) when tappstore detects a CML in the wallet? Or is this a separate list in the [Mining](Mining.md) section?

The next **Invest tab** is specifically for users who want to buy token shares of TApps and both types of CML miners. We don't have to worry about naming this "entities" or something else if we just call this **Invest** as it tells the user what to do here.

## Mining and Leaderboard

The following is for the [Mining](Mining.md) page container.

From the existing **My Assets** page, these can move over to the new [Mining](Mining.md) page:

![\_CML-My-CML](https://user-images.githubusercontent.com/86096370/172456179-4b8f732d-31fe-4a32-8d82-74001ecba3aa.png)

* My hosted TApps (mentioned that we're moving away from host staking tokens, so would show the TApps they're hosting and TEA rewards)

In discussion with Kevin, the following aspects will be deprecated:

* Locked TEA (represents TEA that must be locked for purpose of mining). No longer necessary, they'll directly buy the bonding curve token for their mining node.
* Public Service rewards (will no longer accrue to app, will go directly to miner's wallet).
* **CML Mining** page will no longer appear in mining section. They can view a list of all CML in the Invest (Entities) page.
* My Camellia (will show up directly in user's wallet). Users will have to turn on "NFT autodetect" in their Metamask wallet and view their CML in their wallet:

<img width="773" alt="Screen Shot 2022-06-07 at 3 58 43 PM" src="https://user-images.githubusercontent.com/86096370/172497011-d79685aa-4d53-4fea-b952-e81a076169b8.png">

**[Leader_board](Leader_board.md) tab** This will be deprecated by the time mainnet launches / only for testnet purposes.

Note we can keep **Marketplace** tab as well (it can just redirect to new Marketplace TApp) or it can just be a link in the [Mining](Mining.md) section.

## Help tab

Help is also where the "Welcome" type of information migrates to. We want users to be able to find it, but not be the first thing they see all the time which would just induce frustration / something they would have to automatically ignore and click over to something more useful

# Account summary

Always stays below Nav Bar
Included in one line:

* Address (hex) | TEA Balance (was called transferrable TEA) 

# Container

The content in the container is based on the top level nav bar page selected.
