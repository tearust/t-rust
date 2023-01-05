TEA Swap is a decentralized asset exchange (DEX) that runs an [[Automatic Market Maker(AMM)]] to trade any [[fungible_assets]] instantly. It is not an **order book** type exchange. Instead, it runs similar to Uniswap algorithm but different in many ways:
- TEA Swap is not a smart contract, it doesn't run on blockchain. Therefore its gas fee is almost zero, and settled instantly.
- TEA Swap works with any fungible tokens, no limited to ERC20, nor blockchain based assets. Those assets could be fiat or CBDC as long as it supports [[decentralized_escrow]].

# Key concepts
![[Automatic Market Maker(AMM)]]

![[fungible_assets]]

![[decentralized_escrow]]
# Work flow

## Top up
TEA Swap can only handle assets that "topped up" to the exchange. Those assets are usually called "wrapped asset" once topup. The reversed operation is called "withdrawl", that means take the wrapped asset out to a regular asset. 

## Fill in liquidity pool
[[Automatic Market Maker(AMM)]] is rely on liquidity pool from [[liquidity_pool_provider]]. Before any two assets can be exchanged, some provider need to fill in the liquidity pool. Those provider will be incentivized by earning exchange fee. They also take risk of impremenent loss.

Once the liquidity pool is setup, anyone can start trade those assets

## Submit exchange transaction
The trader can see real time exchange rate calculated by the TEA Swap algorithm. The trader submit an exchange transaction to the system. The code will calculate the fee and converted target asset amount instantly. The trader will received the wrapped target asset immedaitely.

## Withdraw
Trader can withdraw the wrapped asset from the TEA Swap. Once withdraw, the trader will receive the asset (not wrapped) in his original bank account.
