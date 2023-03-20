# TApp Billing

The calculus for a TApp that must pay for the txn fee and memory tax is a bit different depending on what rate it charges for TApp usage.

* If the TApp usage fee charged covers the txn fee and memory tax, then the excess is paid as a dividend profit to the TApp’s bonding curve token holders.
* If conversely the usage fee charged isn’t enough to cover the state machine related costs, then the TApp will be losing money every time an end-user uses the app. This could especially be the case with not-for-profit TApps that don’t charge a usage fee.

Visually the revenue and expenses for the TApp portion of the end-user’s payment looks like this:

![TApp Billing](https://user-images.githubusercontent.com/86096370/218186872-384f76cf-4695-4b0f-b9b4-7c5c484f162c.png)

1. The TApp charges a usage fee taken from the end-user’s payment that goes into the accrued balance account.
1. The txn fee and memory tax owed by the TApp draws from the accrued balance into the **collection pool**.
1. The collection pool is settled at regular intervals (5–20 minutes) into the wallets of the state maintainers who receive the txn fees and memory tax as revenue.
1. Settlement from the accrued balance goes into the TApp’s bonding curve at regular intervals of between 5–20 minutes. Because this happens post-expenses, this accrued pool could either be positive (resulting in a dividend distribution as shown in the graphic) or negative. If the accrued balance is negative net of the txn fees and memory tax, then that shortfall is levied as an expense to the TApp’s bonding curve token holders.

This latter scenario is exactly the reverse of what happens when TApp usage generates a profit. Instead of new TApp tokens being generated and sent to existing TApp token holders as a dividend, TApp tokens are taken from the existing token holders and liquidated for their TEA value to pay the necessary state machine expense. This would eventually lead to the bonding curve token being liquidated.

These actions — dividend payouts for profits and token dissolution for expenses — occur at regular settlement intervals. Because the bonding curve can remove tokens from among its holders, TApp token investors should perform due diligence on the TApp to ensure expenses are covered by the TApp usage fee. A TApp token is just like any other investment that has risks, and dividend payouts along with token subtractions are part of the nature of the bonding curve.

More information is available in our [billing-faq](t-rust/obsidian/_gitbook-dev-docs/030_billing/billing-faq.md).
