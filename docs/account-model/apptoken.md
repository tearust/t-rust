# AppToken is ERC 20 defined in ETH blockchain

Instead of our original design, the TAppToken is now a standard ERC20 that defined in ETH chain.

# AppToken can be transferred between ETH smart contract and TEA Layer2

Using Topup and withdraw, user can transfer tapptoken between layer 2 and eth smart contract.

Because the low gas fee and fast transaction, the token in layer2 can be used to micro payment between applications. But when they want to use Uniswap or other exchange, they will need need to withdraw back to ETH smart contract. Of course, the ETH gas need to be paid.

# Optional bonding curve in smart contract for TAppToken

Instead of standard ERC20 smart contract, we can use bonding curve algorithm to set the price. So that the buy / sell between USDT and TAppToken can be handled using bonding curve smart contract.

The benefits are:

* They can use USDT to buy / sell TAppToken directly. No longer need to convert to TEA.
* Automatically supported by major CEX DEX. TEA Project doesn't need to design a DEX for them.

## The bonding curve can be specially designed

In stead of traditional theta, we can design two different buy and sell curves.
More detail on this design in another article. The goal is to make more up front development fund support at early stage as well as not breaking the reservation rule. The theta reduce while total support increase. Because more development funds needed at the beginning. Once the total supply reach a higher number, the demand for theta decreases. The earlier invest pay more theta to support the development. But on the other hand, they get lower initial price and much larger gain. The buy curve doesn't start from zero, but from an initial value.
Example is https://github.com/emmavz/cypher/blob/main/tokenomics.md

# The TApp needs to pay the TEA project gas fee in TEA

This is the only usage of TEA at this new design. The TApp cannot pay gas fee using their own token. They have to pay TEA.

They can sell their own token in market to get USDT/ETH then swap to TEA to pay gas.

# The TApp user pay their TAppToken to consume, instead of TEA.

In our original design, we require every TApp to use TEA as the currency. This is hard as TEA is not a universal currency at the early stage. This make the barrier of cold start pretty high. So we change to allow consumer to pay TAppToken when using TApp. This can encourage TApp developers to build application on TEA. The price of their token is priced using USDT stable coin. Which is more faimilar for most users.

The price of the TAppToken can be defined using Bonding curve if the app owner prefer. But they can choose wotherever pricing model base on their own need. As a TEA platform, we only care about the gas fee paid in TEA.
