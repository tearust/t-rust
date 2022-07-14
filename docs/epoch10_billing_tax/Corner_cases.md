# Maintainer income cannot cover tax

If the maintainer set a high self estimate seat price but the income (from memory tax and txns fee) is too low to cover the maintainer tax. 

Because every seat owner need to pay a [Seat_owner_deposit](Seat_owner_deposit.md) when buying a seat. The tax is paid from this deposit.

## Seat_owner_deposit cannot cover tax

In the case that the maintainer do not reduce the estimate price and keep let the tax paid from the [Seat_owner_deposit](Seat_owner_deposit.md), the deposit will be keep dropping until some time reach zero.

If at any time the [Seat_owner_deposit](Seat_owner_deposit.md) cannot cover the tax. the Seat will consider "forced to" owner give up to the DAO. This means the seat is owned by the dao and re-priced to zero. Anyone can buy it at any price above 1 T. Al the remaining deposit will paid the tax even cannot cover full price. The owner is no longer the owner. 

# Distribution pool cannot cover public service

We preset a minimal pool size [Distribution_pool_overflow](Distribution_pool_overflow.md). At any time of a day, when public service trying to tranfer fund from [Distribution_Pool](Distribution_Pool.md) but its balance is zero,  a one-time transfer from the [Genesis_block_miner_reward_reserve](Genesis_block_miner_reward_reserve.md)  will be called to transfer 1000T, so that the [Distribution_Pool](Distribution_Pool.md) balance is enough fund to pay public service.

# Genesis block miner reward reserve runs out

If the [Genesis_block_miner_reward_reserve](Genesis_block_miner_reward_reserve.md) runs out, it runs out. We cannot mint new TEA to fill in.

If this happens, our tokenomics design has problem. The project is likely fail..

# Corner cases

See [Corner_cases](Corner_cases.md)
