# AppToken is pure security token that exists inside TEA Project layer2

Any TApp can have its own AppToken. 

It is a security token issued by the bonding curve.

It is NOT ERC20 token. So it cannot be sold as a regular token. Owners can only sell it in the TEA Project Bonding curve for TEA token.

This to reduce the legal risk. 


# The bonding curve

In stead of traditional theta, we can design two different buy and sell curves.
More detail on this design in another article. The goal is to make more up front development fund support at early stage as well as not breaking the reservation rule. The theta reduce while total support increase. Because more development funds needed at the beginning. Once the total supply reach a higher number, the demand for theta decreases. The earlier invest pay more theta to support the development. But on the other hand, they get lower initial price and much larger gain. The buy curve doesn't start from zero, but from an initial value.
Example is https://github.com/emmavz/cypher/blob/main/tokenomics.md

# The TApp needs to pay the TEA project gas fee in TEA

This is the only usage of TEA at this new design. The TApp cannot pay gas fee using their own token. They have to pay TEA.

They can sell their own token in market to get USDT/ETH then swap to TEA to pay gas.

The billing system will charge the following fee
- [[Memory_tax]]
- [[Transaction_cost]]
- Other 3rd pay payment such as IPFS

# The TApp user pay their TAppToken to consume, instead of TEA.

In our original design, we require every TApp to use TEA as the currency. This is hard as TEA is not a universal currency at the early stage. This make the barrier of cold start pretty high. So we change to allow consumer to pay TAppToken when using TApp. This can encourage TApp developers to build application on TEA. The price of their token is priced using USDT stable coin. Which is more faimilar for most users.

The price of the TAppToken can be defined using Bonding curve if the app owner prefer. But they can choose wotherever pricing model base on their own need. As a TEA platform, we only care about the gas fee paid in TEA.

