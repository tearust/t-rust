# UI
A new tab only contains Global state maintainer CML on sale

Header is common area. Information in this area
- UTC time. This shows when is the UTC new day starts
- Last daily tax
- Last daily income from Memory Tax
- Total active CMLs count

Below the header, there is a table for all type A CMLs

All type A CML are listed here. Every CML is row. Columns are
- ID: The id of the CML. 
- Current owner address. clickable to a new browser tab with TAppStore's User Detail page. This page shows the detail public information for this owner.
- Status: [[Maintainer_CML_Status]] Inactive / Pending / Active
- Current self estimate price. If owner changes price, it shows here immedaitely. This is the default sorting column, low to high. 
- Last self estimate price before update. if owner change price, the old price shows here imemdaitely. 
- Last price change time. When the owner updated the price in UTC.
- Last sold time: When this CML was sold last time.
- Last sold price: What price this CML was sold last time.
- Action: Buy, Update Price(only for owner), Give up ownerhsip (only for owner)

# Rules
## Transfer ownership at most once a day
 Every day, a CML can only be sold / transfer owner once. 
If a CML is sold today, its status changes to Pending. No more price accepted today. 

## Minimal self estimate price
We set 1T as the minimal self estimate price. Owner cannot set to a price lower than this value. 
## Owner can give up ownership
If owner set the self estimate price to the minimal but still no one want to buy, he can give up the ownerhship. In this case, the CML is back to the "inactive" status that owned by nobody. No one earn the memory tax and pay income tax on it. Anyone can buy it as Zero TEA price. But after purchase, he has to set the estimate price at least to minimal price

## Change self estimate at most once a day
If the owner changed the self estimate price, he cannot change it today. The "update price" button should be disabled for the rest of today.

## If price changed during a day, tax is based on the higher value of the current and orginal price
If owner changed self estiamte price. No matter changed it lower and higher, at the end of the day, when Global Maintainer Tax is calcualted, it is based on the higher value of those two.
The next day, it will use the latest value if no further change.

# Workflow
## Buy
If status is active, anyone can pay a higher price to buy it. Buyer click the "buy"action button. As long as the price is higher than the current self estiamtion, the deal is made. The CML status changes to "Pending". No one else can buy this CML today. The new price is currently hidden on UI, so no one expect the buyer know the real price. The second day, (we can use UTC 0:00 as a starting point of the new day) this CML is changed to the new owner. the price got updated.

Existing owner cannot buy his own CML. 

## Transfer cron job
 sale deal can confirm immediately but the deal close at the end of today. As long as the sale confirm, the amount of sale price will be transferred from buyer's layer2 account to the escrow account. At the end of today, ownership transfer and fund transfer will happen in the cron job as a single transaction. After this transaction, the CML has new owner, the original owner get the fund.

In the next day, the new owner will receive the new day's memory tax income and also pays the maintainer income tax.

## Update price
Only one change of price can be made in a day. As long as a new time change is made, the action is disabled. 

Only owner can click the Update Price button. For other users, this button is hidden.

When the button is active, owner can click it to set a new estimate price in a modal.

The new change will take affect immediately and show in the estimate price. Also the previous price is show on the UI too.

The price change time also recorded and show in the UI

## Ownership transfer happens in layer1
This is the standard ownership transfer of CML which happens in layer1. The same as hosting CML. 

Beacuse the Global CML is always on sale, there is no need to have a "list on market" action for Global CML (A CML)
# Tax collection and payment
Global income and tax pays once a day.

## Memory tax is stored in Global Pool for the day 
The Memory tax is not paid to maintainer at the real time. All memory tax will be collected to the Global Pool first.
## Calculate the tax every maintainer should pay
At the UTC 23:59, the cron job starts to compute tax.
``` 
const tax_rate = 0.01;
total_memory_tax;//input value 
total_number_of_maintainer_at_the_beginning_of_today;// count from database

let every_maintainer_memory_tax_income = total_memory_tax / 
	total_number_of_maintainer_at_the_beginning_of_today;
for every_maintainer in all_maintainers {
	let estiamte_price =  max(every_maintainer.current_estimate, every_maintainer.estimate_at_beginning_of_today);
	let tax_amount = tax_rate * estimate_price;
	let every_maintainer.revenue_today = every_maintainer_memory_tax_income - tax_amount;
}

```
Use the logic ablove to calculate how much a maintainer can get and how much tax he should pay.

## Payment to the CML owner
Both income and tax should be associated with the owner of the current day not the next (new) day. If someone bought a new CML, he doesn't get income or pay tax for the previosu day that he has not officially own it yet.

To make the logic simple, make sure **Calculate tax and income before transfer ownership**. Therefore the payment can be done before any ownership transfer happens.
