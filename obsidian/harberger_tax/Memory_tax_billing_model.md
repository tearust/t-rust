# Understanding Harberger tax in Global state machine
## Limited public resources
There are two limited public resources that we need to protect using Harberger tax
- The state memory.
- The global state machine nodes (used to be  A-CML nodes)

Both of the above items are limited and shared by all TEA members. We are trying to design the fair policy to everyone, as well as very high efficient on utilize the public resources. Whoever occupy the limited resources but did not utlized to the max performance will pay a high tax, or forced to sell it to someone else can utilize it. 
# Pay to reserve state memory
App need to buy a memory in size from bonding curve before use. 
The price of the memory is based on a bonding curve. 
The bonding curve make the price going higher when the remaining memory drops. See [[memory_bonding_curve]] 

> Where does the purchase money go?
The purchase money will not pay to anyone. It will be locked inside the bonding curve as a  reserve. When apps selling the memory back to the bonding curve, the reserved money is paid to the seller.

# Global state memory tax
After the app owns the memory they bought, the app need to continuously pay the memory tax for keep the ownship of such memory. 
![[Pasted image 20220618165650.png]]
## Rules
The Apps pay the memory tax which is based on the current market price (when others buy new memory now).

In this case, the usage fee is similar to Harberger tax. We can set the memory tax rate to 1% of the current market price of 1M memory per day. [[how_to_set_memory_tax_rate]]. 

The tax is paid to Global state machine nodes. ![diagram](https://user-images.githubusercontent.com/1761809/175086867-4f2a6caa-1b68-4e44-9f2b-a504e6f4906c.png) They will need to pay [[maintainer_tax]] later.
# Use case exmaple

Day_0. The total memory of state machine is 1000M. The init price of memory is $10 / MB. App_A estimate it need 2M of state storage. So App_A pays $20 (may be a little higher due to bonding curve, just make it sample to set to $20) and receive the right to occupy 2M space in the state memory. The remaining memory is 998M. The price is increased to $11/MB based on the curve.

Day_1. App_A pays $0.22 for usage fee. 0.22 = 1% * $11 * 2(MB) per day
Day_2. App_B wants 100MB memory. Calculated by the curve, App_B pays $5000 for the 100MB. It is not $11 * 100 = $1100 because bonding curve is not flat. $5000 is an example number.  After the purchase, the price per MB is $100
Day_3. The daily usage fee is adjusted to 1% * $100 = $1 per MB. So App_A needs to pay $1 * 2MB = $2 today. Comparing the previous daily fee $0.22, this is a significant increase.
Of course, App_B needs to pay $500 today for the usage.
Please note there is only 498 MB free memory left. The price per MB is $100, tax is $1
Day_4. App_A found it is too expensive to use 2MB because of paying $1 per day. They refactored the code that improve the memory usage. They now only need 1M RAM.
They sell 1M to the market at today's price $100. They earn $90 (their investment is $10)
Now the remaining memory is 497 MB, the price is $98/MB, the tax is 0.98/MB/Day

# Economy up/down turn impact
The memory of state is a limited resource that shared by all tapps. We will need to adjust the price and tax to make sure it can be maximized utilized.

Early adopters can get lower price when they buy the RAM at the beginning when price is low. But if they did not make enough profit, the up coming tax may be higher and higher. If the economy going us, they can still be profitable buy selling their unused memory back to the bonding curve. Just like the App_A earn $90 by selling 1M RAM.

In econmy down turn, the apps will have less user, and inturn less need of memory. They can sell the RAM back to the bonding curve. The price of memory will reduce. Therefore the tax (!% of the selling price) is lower too. The cost of keeping memory is lower. It would be easier for apps to survive in the econmy down turn.

When econmy is booming, there are more apps join the TEA network. They will need to buy memory. This makes the price of memory going up. Also the tax going up. The earlier adopter has options to reduce memory usage and sell the memory to new comers and appreciate by capital gain. Or if it can earn more from their customer, and still affordable to pay higher tax.

