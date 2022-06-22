# Price
The curve can be a function like
price = total_ram / remaining_ram * const

The price goes up when remaining_ram drops. It will become infinity if remaining_ram is close to zero. That means no one can afford to buy. So the memory is never used up.

The init price is the const. as total_ram == remaining_ram. 
Then there is only half of ram remaining (that means half in use), the price is double of const. if remaining is only 1/4, the price is 4 x const.

# Reserve 
The fund to puchase memory will be locked in the curve. We can set the bonding theta to 0. So there is no difference between buy and sell curve.

