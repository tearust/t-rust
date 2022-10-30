When DAO generate new CML seeds, everyone can join the auction in this tapp.

It is the same app as previous marketplace.

Differences are
- Only seeds allow to be sold here. Matual CML can be trade in OpenSea
- Once deal is made, the ownership transfer happened in layer1. Maintainer sign ETH txn to transfer the ownership of this seed.

# Mint seed
Iin Epoch11, sudo or maintainers can mint new seeds manually. In the future, new seed mint based on algorithm of birth control.

When new seed is generated, it is owned by the sudo. After auction, the winner will be the new owner. the maintainer sign layer1 txn to transfer seed ownership.

There is no UI to mint seed. We use Hardhat scripts to generate new seeds to Ethereum

# Sudo put seed on bid

Only Sudo can see this UI. This UI has an input box for CML id and a Confirm button.  
Input box of a CML id. This need to be a new mint seed that owned by the sudo. Otherwise, fail.

After confirm, this CML id added to a SQL table.


# Seed list
The UI of the Seed Market query SQL to get the Seeds CML id currenly added into the bid list.
Show the CML info in this list.

# Bid price, and grace period
We use a new rule to bid. Click [[seeds_bidding 1]]

# Where the money goes ?
The price the winner paid for the seed goes to the TAppStore consume account.

# In_app purchase
All action in this app is paid in_app purchase. So far, only one action: Bid (set a new price)

# Deposit

Bidder pays deposit during the auction. When new bidder take over the highest position, the deposit will be refunded to the loser

# Deduct spending limit when deposit


# Restore spending limit when refund
When refund, the deducted spending limit need to be restored.

# Winner fund transfer
The deposit fund is transfer to TAppStore consume account.
If there is any remnining, refund.


