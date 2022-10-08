# Prepare before testing
this is done by dev team. after a new deplooyment but before testers start/

## Using scripts to send TEA, Coffee, CMLs to testers
Based on the assets list from epoch10.2, we create a js scripts. Run this hardhat js scripts to send tokens to testers. 

## Setup vesting schedule
This scripts also set the Vesting schedule. Ref issue #1271

Note:
This scripts may need to change if deployed address changed.

# TAppStore

wallet.teaproject.org is still the portal of TAppStore. But user may use any hosting node IP to join.  They can find the entry from the TApp tabs inside tappstore. TAppstore is also one line in the tapp, one of the tapps. so it has it is own launching ipfs url as well. 

## Launch tapps
There should be at least the following Tapps in the list
- TAppStore
- Leaderboard
- Miners' portal
- Harberger

Click to launch tapp, by default from this wallet.teaproject.org side. BUT, if you are running tapstore from other IP, it should open at that IP address.
>how does user know the IP of other hosting nodes?
>Click the app link should pop a box with URLs

## Set spending limit

## Set spending limit at app launch

if the spending list is lower than 10T, show an warning at app launch box, click to set the min-spending-limit then continue.

## If spending limit is too low, operation will be rejected in that app.

Right now, every action cost 1 TEA. when the spending limit lower than 10T, the app may reject action request. 

## Investment buy / sell
Verifythe number, also check the log to make sure the transaction amount correctness

## Sell all token won't auto remove tApp
In epoch11, if an app has zero total balance is still ok. It will not be removed automatically. but if app has any expense that it cannot cover. it will be removed when handling expense cron job.

## Account transfer layer2 TEA
please also test edge case and failed case. verify the error message makes sense
## Topup and withdraw layer1 TEA
please also test the fail cases. such as overdraft. Verify the error message makes sense

## Verify deposit
In Harberger app , buy a seat. You will see 1000T deposit here in the TAppstore. When the user sell the seat, the reposit should go away.

## Verify the tapp accute balance
When a tapp has income (in hidden_consume_account) beofre it is shared as dividend, it will be shown here in the table column. Please make some operation, such as buy / sell tokens. we should see the accute balance changes. Current most action costs 1 TEA. some action may cost 2T because it is more than one transactions.

## Check log
Verify the amount for every action. Calculate the result's correctness.

# Leaderboard

## Register leaderboard
this will cost 1T, also cause the spending limit reduct 1 TEA.

After register, this user should be listed in the table. anyone else should see it.

There is no way to remove from the leaderboard

## Leaderboard app account page
Please verify the spending limit.


# Harberger 
## Buy seat
This is the same operaiton as epoch10.
Note that Ethereum is 12seconds per block, so we reduce the requied blocks numbers interval. otherwise the round would be too long (one hour)

## Sell seat, give up seat
Please verify the deposit get refunded. YOu can see it from both Harberger account or TAppStore account.

## Seat maintainers income in cron job
After a while (approx 30 minutes), a cron job will run to pay maintainer tax, income, dividend. Please check Log page for numbers, verify.

## Miners portal
## Buy CML seeds
We do not have such feature in epoch11 yet. Right now,, anyone wants a CML seeds need to be done manually.

## Trade CML in OpenSea
this is out of TEA Project. it has to be done in OpenSea on Goerli

## Plant
The same workflow as epoch10, but no need to click Metamask to sign.
Need to setup CML entity before planting.

## Unplant
the same.


# Gas fee
There is 1 CENT gas fee paid by end user to hosting node. This gas fee is used to cover the cost of hosting node. (Originally call B nodes)

# In_app purchase
Most user action will cost 1T as in_app_purchase. It is paid by end user to the TApp's bonding curve.

# Txn exec fee
There is 1 T "txn exec fee" paid by TApp to state maintainer

# Memory tax
End user cannot see this, it is paid by TApp to state maintainer

# Maintainer tax
It is a harberger tax. Paid by the state maintainer to Global token

# Dividend
Cron jobs run to collect tax, pay dividend. The log view will show the detail for every one.
