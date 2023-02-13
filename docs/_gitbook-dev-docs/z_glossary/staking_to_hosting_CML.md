# Staking to Hosting CML

Staking to a hosting CML mining node is a way for users to earn a share of mining revenue without having to setup a mining machine. Each active hosting CML mining machine will have its own stake token associated with it. Owning a miner's stake token entitles the holder to earn a percentage of that miner's revenue.

* CML miners earn the gas fee paid by the end-user to use their hosting infrastructure to run TApps.
* CML miners also earn a public service reward as well as any surpluses above their public service reward payout from the Harberger Tax paid by the state maintainers.

In addition to earning dividends, hosting CML token holders will also benefit from the price appreciation of the token itself. Mining stake tokens are issued on a bonding curve, which means that the price of the token increases as the supply increases.

* As TEA mining rewards are earned, the net percentage after the miner gets their allotted (theta %) share is injected into the bonding curve. This results in an increase in the number of mining stake tokens which are distributed proportionally as a dividend to existing staking token holders.
* As TApp token mining rewards are earned, the net percentage after the miner takes their share is distributed proportionally as a dividend to existing staking token holders. Note that because no TEA is being injected into the mining stake token bonding curve during TApp token distributions, this event doesn't change the supply or price of staking tokens as it does when TEA is distributed to staking token holders.

## Revenue split between miners and mining stake token holders

`#########`
**TODO** The following section is speculative:

* What is the split between miners and token holders?
* Is the portion that goes directly to the miners considered theta just like in the TApps (and thus will be the difference between the buy and sell curves of the mining stake token bonding curve)
  `#########`

All mining revenue will be split between the miner and mining stake token holders as follows:

* 50% will go to the miner.
* 50% will go to the staking token holders. Note that the staking token holder group also includes the miner who will own some of the mining stake tokens.

## Private CML don't have stake tokens

Private CML are the CML that users deploy to their home TEA boxes to allow them to run on the TEA network. Because these machines don't allow random end-users to utilize their hardware, these miners currently only earn Availability Attestation rewards. Availability attestation is a service private CML provide which allows them to be online or offline as they wish. This is compared to the much more stringent requirements to be a hosting CML mining node that must always be online to host TApps or else suffer penalties. Because private CML machines aren't always online, they only earn direct fees from providing an ancillary service to the network. Because these private CML are not actively providing hosting services to TApps, there's no consume function revenue to share with stakers.
