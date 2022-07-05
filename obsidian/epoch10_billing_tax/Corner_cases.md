# Maintainer income cannot cover tax
If the maintainer set a high self estimate seat price but the income (from memory tax and txns fee) is too low to cover the maintainer tax. 
We will set a negative number (special treatment in the data structure) means that the maintainer owe the tax. Starting from today, the days_of_default is set to 1.

The next day, if the maintainer has enough income to cover tax, or If the maintainer pay off the owe tax from his personal wallet, the default is clear. days_of_default is set to 0. The tax is paid the next day.
But if the maintainer does not pay the owed tax the next day, the days_of_default will be added one, to 2. 
If the maintainer cannot pay the tax for the next 5 days. the days_of_default reaches 7, the grace period is run out. The slash happens

The slash is:
- This maintainer seat is force to owned by the sudo. Reset the seat price to zero, any one can claim to own.
- If the maintainer has balance in TAppStore, the balance will be used to pay the owed tax. If there is no remaining, this won't work.

# Distribution pool cannot cover public service
We preset a minimal pool size to 1000T. At any end of a day, if the distribution pool balance is lower than 1000T, the [[Genesis_block_miner_reward_reserve]]  will transfer enough fund to topup the balance back to 1000T.

If in the middle of a day, all 1000T plus the income from [[Collection Pool]] cannot cover the public service, this corner case happens.

Solution:
Trigger the transfer from [[Genesis_block_miner_reward_reserve]] 1000T immediately.

We should try to avoid this corner case by increase the minimal pool size from 1000T to a larger number.

# Genesis block miner reward reserve runs out
If the [[Genesis_block_miner_reward_reserve]] runs out, it runs out. We cannot mint new TEA to fill in.

If this happens, our tokenomics design has problem. The project is likely fail..
