# Global State Maintainer income tax calculation

Assuming we have cap of 100 global nodes to maintain the global state. All of their income is the memory tax.
As all the global nodes are the same. They just share the memory tax income evenly.
Running a global node is profitable so that we will need a Herberger tax to those nodes. This is called maintainer income tax.
The maintainer income tax pays to all stakers who bought the Global bonding curve.
The amount of maintainer income tax is daily 1% of [self_estimate_price](../harberger_tax/self_estimate_price.md) of maintainer's CML.

The maintainer needs to pay a Maintainer Income Tax. This tax is deducted from the maintainer revenue before paid. So the maintainer will just receive the "evenly shared memory tax minus his maintainer income tax" as his revenue. 

The maintainer income tax is collected together to a temporary pool that pays the public service first. The public service is paid at real time. [What_if_public_service_cost_run_off_maintainer_income_tax](../harberger_tax/What_if_public_service_cost_run_off_maintainer_income_tax.md)?. At the end of the  2nd day, the remaining fund in this temporary pool will be fully paid to Staker in [Global_bonding_curve](../harberger_tax/Global_bonding_curve.md) See ![diagram](https://user-images.githubusercontent.com/1761809/175086867-4f2a6caa-1b68-4e44-9f2b-a504e6f4906c.png)

The number 1% may be changed by the DAO voting. 

# Use case example

The cap of number of the [Global_state_maintainer_nodes](../harberger_tax/Global_state_maintainer_nodes.md) is 100. Income tax rate is 1% (0.01) Every day the memory tax income is $1000. The minimal value of a node is $1. 

Day_0, no one own any node. The remaining number of maintainer nodes is 100. 

Day_1, Alice claim a [Maintainer_seat](Maintainer_Seat.md) at [self_estimate_price](../harberger_tax/self_estimate_price.md) of $1 which is the minimal value. Since no one own such [Maintainer_seat](Maintainer_Seat.md) at the beginning, Alice doesn't need to pay anyone $1 for owning such a CML. Because the [self_estimate_price](../harberger_tax/self_estimate_price.md) has a minimal value of 1T, she cannot claim it value of 0. Although she actually pays $0 to own it.
Day_2. Alice is the only node. So she earn total 1000T from the memory tax income (no one else share with her). She will need to pay $0.01 = 1% * 1T as income tax. This income tax will go to the Global staker bonding curve as consume, eventually share by all staker holders. Her revenu is $999.99. Of course, very profitable.

The $0.01 income tax goes to the [Global_bonding_curve](../harberger_tax/Global_bonding_curve.md) as consume dividends share to all init stake holders. They get passive income. No doubt at early stage, the income is too low to cover the basic public service. We will have special [cold_start_funding_support](../harberger_tax/cold_start_funding_support.md) to make the project cold start.

Day_3. Bob get the second [Maintainer_seat](Maintainer_Seat.md) at [self_estimate_price](../harberger_tax/self_estimate_price.md) of $1. Again, since no one owns such CML at this moment. Bob doesn't need to pay to own it.

Day_4. Alice and Bob share the $1000 evenly, so Alice get $500, Bob get $500..Now Alice pay income tax of  $0.01, Bob pays income tax of $0.01 The total of the income tax is still $0.02. Both Alice and Bob earn $499.99

Day_5, All 100 [Maintainer_seat](Maintainer_Seat.md) are claimed by new miners. They all claim the price to be $1. So that they all share the same income tax of $0.01. No doubt, it is very profitable to run a node, as the income is $10 = $1000/100, after paying $0.01 tax everyone still earn $9.99 revenue every day. The dividend is $0.01 * 100 = $1 pay to stakers.

Because being a miner is so profitable, more miners will join the compete. Let's see what is gonna happen.

Day_6. Charlie was original a staker, when he realized being a miners is so profitable than a staker, he wants to be a maintainer too. but all 100 [Maintainer_seat](Maintainer_Seat.md) are occupied and all of them set the claimed price at $1. Charlie pays $1.5 and buy a [Maintainer_seat](Maintainer_Seat.md) from any of them, say Alice. So Alice get $1.5 and be removed from the [Global_state_maintainer_nodes](../harberger_tax/Global_state_maintainer_nodes.md) list. Charlie starts mining and pay tax $1.5 * 1%= $0.015.
Now everyone else except Charlie pays $0.01 tax. 

Day_7. As Alice lose the [Maintainer_seat](Maintainer_Seat.md) to be a miner, she wants to continue mining, so she pay $2 and buy Bob's. Bob knows that and do not want to lose the mining oppotunity, he increase his self-estimate to $2. So does everyone else. So all the 100 minutes increase the self-estimate to $2. If Alice really wants to buy a [Maintainer_seat](Maintainer_Seat.md) she may have to pay any price higher than $2. Finally, everyone set the [self_estimate_price](../harberger_tax/self_estimate_price.md) to $800. 

The daily tax is 1% * $800 = $8. The memory tax income is $10, so every maintainer has $2 profit. At this point, the profit is much lower than before but still barely cover the maintaining cost. No one else want to pay more than $800 to buy a CML because it will not be profitable. This become a new balance point. At this balance point, the total mainter income tax is $8 * 100 = $800. Being a staker is much profitable than before.

# Automatically adjust the balance between miners, apps and stakers.

When more apps deployed to the TEA Project, the more memory tax collected to the [Global_state_maintainer_nodes](../harberger_tax/Global_state_maintainer_nodes.md). The higher profit earned by the maintainer. If the maintainers do not increase their [self_estimate_price](../harberger_tax/self_estimate_price.md), others (such as stakers) would like to buy a CML at relatively lower [self_estimate_price](../harberger_tax/self_estimate_price.md) to become a maintainer. So that the maintainer has to increase the [self_estimate_price](../harberger_tax/self_estimate_price.md) to keep his CML. As long as the [self_estimate_price](../harberger_tax/self_estimate_price.md) increase, the higher [maintainer_tax](../harberger_tax/maintainer_tax.md) cause the lower profitiability for maintainer, as well as higher  [Global_bonding_curve](../harberger_tax/Global_bonding_curve.md) dividend cause hgher profitable as a staker. So some maintainer may want to lower his [self_estimate_price](../harberger_tax/self_estimate_price.md) to sell CML and become a staker. So the [self_estimate_price](../harberger_tax/self_estimate_price.md) will always be at a reasonable price range that balance the app, maintainer, and staker. 
You can see this whole Harberger tax system automatically balance the miners and stakers and apps. 

# TEA Core Team special rule

In the first two years, for the security reason, we need to keep the TEA core team  to have the control over the state machine. Otherwise, any small bug can cause the whole system down. In order to do that, we will have a 2-year special policy that the TEA core team will always occupy 50% of the Global nodes that cannot be sold. That means there are always less than half of Global nodes are in the market for trading.
