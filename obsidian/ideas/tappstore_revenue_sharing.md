# Gist

We run a cron job periodically distribute the fund in TAppStore's accrued balance to the following pools.
- Pool #1, [[node-subsidy]], Universal basic income. 
- Pool #2, [[Subsidy_acquire_new_user]], The fund to send new users for first time use.
- Pool #3, [[propotional_incentive_to_hosts]]. Based on how much contribution, propotional reward those hosting nodes. This pool is unlimited size. It will use all the remaining TAppStore accrued balance.

## Distribution rules
We run distribution cron job daily.
From the first pool, then second ... till the last one.
Fill the first pool till it is full, then if the TappStore accured balance still remains, fill to the second pool, then third, till running out. Because the last pool has no max level, so all remaining would be fill to the last pool. After the distribution, the TAppStore accrued balance should become minimal. (the minimal is a const value, could be 1 T)

## DAO Reserve fill if Pool1 and Pool2 cannot be filled in full
if the the TAppStore accrued fund not enough to fill Pool1 or Pool2 in full, the DAO Reserved will be used to fill till they are full.

## Each pool has its own distribution rules
You can click each pool's link for how the fund should be distributed in that pool.

## Adjustments of max level of each pool
This can be modified using DAO votes.

