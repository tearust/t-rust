# Rollover from epoch10.x to epoch11

## Step0 Everyone needs to register ETH address before Epoch10.x ends

This has to be done before epoch10.x ends. Epoch10.x is the last epoch right before epoch11.
In Epoch10.x , the register contest UI has a fields to input this user's ETH address. 

Anyone who wants to keep their existing assets and rollover to epoch11 MUST add their ETH address in this leaderboard. Otherwise we will NOT know how to map their assets to the new address. 

In epoch11, we will NOT use the Polkadot address we used before epoch10.

## Step1 Screenshot

We will take screenshot of

* User layer2 assets shows on leaderboard. All layer2 assets not shown on leaderboard will be ignored. This is the Total asset value. NOT the net asset gain. 
* User layer1 TEA
* User layer1 Coffee

Example: Alice has 5 Coffee 100 TEA in layer1 and 900TEA in total assets value in layer2. 

## Step2 Calculate all assets in TEA for every user

Convert coffee to Tea at 1:1000 rate.
Calculate the total Tea for every user. Also calculate the TOTAL_TEA value for the total of all users.

For every user do

* 1/2 his total Tea conver to Coffee at 1000:1 rate
* send Tea and Coffee to this user's layer1 account (ETH address) when Epoch11 starts

Pay attention here: To layer1 not layer2. User need to topup on their own.

Example, Alice from previous step.  Her total asset value is 5 x 1000 + 100 + 900 = 6000T. She will receive 3Coffee and 3000Tea in her ETH address when epoch11 starts.

Assume Bob has exactly the same assets as Alice, and there are only two players in epoch10.x, then the TOTAL_TEA is alice's + Bob's tea = 12000 T

## Step3 Mint NPC Tea and Coffee

NPC Mint 1/2 of TOTAL_TEA as Tea Token owned by NPC
NPC Mint 1/2 of TOTAL_TEA but convert to Coffee (1:1000 rate) token owned by NPC.

Example: NPC mint 6 Coffee and 6000 T.

# Step4 Set the uniswap dex initial Liquidation Pool

Send all Step3 mintted Tea and Coffee to the Exchange as Liquidation pool.

Example: the Uniswap LP has 6 Coffee and  6000 Tea as the initial LP. The exchange rate is 1:1000. 

So when the epoch11 starts, the total TEA is 12000 (6000 in user's address,  6000 in Uniswap LP). The total Coffee is 12 ( 6 in user address, 6000 in uniswap LP )

# Invest to uniswap LP

NPC is the first LP provider. NPC puts 1/2 TEA and 1/2 Coffee of TOTAL_TEA value from epoch10.x to the uniswap LP.

Any user are encourage to invest to the LP as well. We will give them some kind of incentive. But at this moment, we have not figured out how. 

Because every one will have 1/2 of their original assets from epoch10.x in TEA, another 1/2 in Coffee. So they can 

* Go to uniswap to exchange
* Invest to uniswap LP for some kind of LP reward

# In and out fund from/to the isolated ecosystem

Because we made both TEA and Coffee. This ecosystem is kind of isolated.

## Make a few reserved Coffee mint in Genesis

In order to prepare new buyer from outside, we will mint a small amount of Coffee and owned by NPC at the beginning. The number can be set to 1000 Coffee. That is about 1000 USD. I think it would be large enough.

## New user pay BUSD to join the epoch test

We still need to manually receive BUSD in BSC chain, manually mint new Coffee based on 1 Coffee : 1 BUSD. New mint Coffee send to the buyer.

## Existing user sell Coffee and leave the game

If someone want to leave the game. He will exchange TEA to Coffee, and manually ask to redeem BUSD. We manually send him the BUSD on BSC chain, and burn the Coffee. Burn is actually store in NPC account.

# The 1:1000 rate reset in next epoch11. x

The 1:1000 (Coffee:TEA) is the init value when we start epoch11.0.
For epoch11.1 starts, we will use the actualy rate in uniswap exchange. If might be 1:900, maybe 1:1200. 

# At the end of epoch11. 0, how do we withdraw the uniswap LP?

@George, this is a question for you. we probably do not withdraw. leave it there, we will use it again in epoch12.
