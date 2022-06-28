
# State Maintainer Tax
## State Machine Nodes — Revenue & Expenses

When a TApp executes tasks on the TEA network, the TApp developer is responsible for the payments for the following:

**1) [[Txns_computing_cost]] 2) [[Memory_tax_billing_model]]**

These are paid by **app state actors** into a **[[t-rust/docs/epoch10_billing_tax/Collection_Pool]]**

These payments happen either continuously or at regular intervals. For computing costs, the payment stream is continuous as the txns occur. For memory tax payments, this income is moved into the [[t-rust/docs/epoch10_billing_tax/Collection_Pool]] as often as they’re charged (i.e. every 10 minutes, or every hour depending on what’s set by the DAO).

So as long as there is app usage on the network, we can expect there to be money sitting in the [[t-rust/docs/epoch10_billing_tax/Collection_Pool]]

### Meanwhile, the Maintainer Tax Needs Paying Once a Day

Every day, a cron job runs to calculate how much tax is owed by each of the state maintainer nodes (nodes that own a [[t-rust/docs/epoch10_billing_tax/Maintainer_Seat]] license). The tax for each node is (self-assessed value * .01). Each node will have its own [[t-rust/docs/harberger_tax/self_estimate_price]]. This tax amount is added together as part of the cron job daily, and we’ll call this the **aggregate maintainer tax.**

TEA tokens are moved once a day from the [[t-rust/docs/epoch10_billing_tax/Collection_Pool]] into the [[t-rust/docs/epoch10_billing_tax/Distibution_pool]], and the **aggregate maintainer tax** that the miners owe is then paid out use the funds in this [[t-rust/docs/epoch10_billing_tax/Distibution_pool]] once a day.

The [[t-rust/docs/epoch10_billing_tax/Distibution_pool]] is funded daily up to the **aggregate maintainer tax** amount. The [[t-rust/docs/epoch10_billing_tax/Distibution_pool]] goes to pay both 1) [[teaproject/docs/_tapp-tutor/public_service]] rewards (priority) and 2) dividends to [[t-rust/docs/epoch10_billing_tax/Global_token]] holders (if any TEA is left over after paying the public service rewards).

-   The [[t-rust/docs/epoch10_billing_tax/Distibution_pool]] has a funding mechanism that every day makes sure it’s funded to at least 1000T. This amount is set by the DAO and is set to an amount expected to cover the daily [[teaproject/docs/_tapp-tutor/public_service]] rewards.
-   Because there are some instances where the daily total in the [[t-rust/docs/epoch10_billing_tax/Collection_Pool]] won’t keep the [[t-rust/docs/epoch10_billing_tax/Distibution_pool]] funded to the minimum 1000T, in these instances a **reserve pool** is tapped. 
-   The [[t-rust/docs/epoch10_billing_tax/Distibution_pool]] taps the **reserve pool** once a day to top itself back up to the minimum 1000T reserve in lieu of there not being enough funds in the [[t-rust/docs/epoch10_billing_tax/Collection_Pool]].
-   The **reserve pool** pulls initially from an allocation in TEA’s 100M [[t-rust/docs/harberger_tax/pre_mined]] tokens. It’s replenished through CML purchases as the TEA used to pay for CML is then sent to the **reserve pool** fund.
-   The [[t-rust/docs/epoch10_billing_tax/Distibution_pool]]’s priority is to pay the [[teaproject/docs/_tapp-tutor/public_service]] rewards. If there’s money left over after paying the public service rewards, then this amount is paid out as a dividend to [[t-rust/docs/epoch10_billing_tax/Global_token]] holders  (i.e. those who’ve invested in the global state maintainers through purchasing a token issued on a bonding curve). The amount in the [[t-rust/docs/epoch10_billing_tax/Distibution_pool]] at the end of the day must be greater than 1000T in order for the dividends to be paid to the **global token holders**. For example, if there’s 1500T at the end of the day in the [[t-rust/docs/epoch10_billing_tax/Distibution_pool]], then 500T will go as dividend rewards to [[t-rust/docs/epoch10_billing_tax/Global_token]] holders and 1000T will remain in the [[t-rust/docs/epoch10_billing_tax/Distibution_pool]].

Also note that each node has a different [[self_estimate_price]] of its value, so they will pay a different tax amount depending on their unique self-valuation. Because of varying self-valuations for the Harberger tax among nodes, some miners might be in profit while others are in debt relative to their income.

### Practical Money Flow for the State Machine Nodes

If a miner runs a state machine node, they’re primarily interested in how they’re going to make money. So we have to simplify the following diagram which is useful for seeing how the actual money flows between app state actors and state machine node maintainers:

![](https://cdn-images-1.medium.com/max/1200/1*PvkBOMObbJlbf0PCkoDEYg.png)

For state machine miners, first thing to know is that they don’t need to worry about what the [[t-rust/docs/epoch10_billing_tax/Distibution_pool]]is doing, or if it’s short on funds. The interplay between the [[t-rust/docs/epoch10_billing_tax/Collection_Pool]], [[t-rust/docs/epoch10_billing_tax/Distibution_pool]] and the **reserve fund** is not something that a miner needs to be concerned with. They only need to pay their maintainer tax that’s based on the self-assessed value of their [[t-rust/docs/harberger_tax/Global_state_maintainer_nodes]] seat license.

Miners will need to keep track of the income that accrues to them (their share of the [[t-rust/docs/epoch10_billing_tax/Collection_Pool]]) as any shortfalls versus the tax that they owe must be paid back within 7 days or they risk losing their state [[t-rust/docs/epoch10_billing_tax/Maintainer_Seat]]. When they pay back the funds, it’s added on top of what’s already in the [[t-rust/docs/epoch10_billing_tax/Distibution_pool]] for that day. For example, if multiple miners pay back their debt on the same day, the [[t-rust/docs/epoch10_billing_tax/Distibution_pool]] is boosted that day and has an even better chance of paying out dividends to [[t-rust/docs/epoch10_billing_tax/Global_token]] holders (who only receive dividends after the [[teaproject/docs/_tapp-tutor/public_service]] rewards are paid out).

Money flow from miner’s perspective: If [[t-rust/docs/epoch10_billing_tax/Collection_Pool]] amount < daily **aggregate maintainer tax** owed, then each miner owes a debt. This debt must be paid off in 7 days or else their mining license is forfeited.

If [[t-rust/docs/epoch10_billing_tax/Collection_Pool]] amount > daily **aggregate maintainer tax owed,** then the amount leftover in the [[t-rust/docs/epoch10_billing_tax/Collection_Pool]] is sent out proportionally as profit to the state maintainer nodes.