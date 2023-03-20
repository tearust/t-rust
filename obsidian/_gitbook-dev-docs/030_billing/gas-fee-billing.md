# Gas Fee Billing

It should be noted at the outset that the TEA Project’s billing and settlements happen at different times. The billing record is kept in real time while the settlement of the billing cycle occurs every ~ 5 minutes (subject to change).

Let’s first focus on how payments are routed for the gas fee. In graphical form:

![Gas Fee (in depth)](https://user-images.githubusercontent.com/86096370/218186875-93b7aaa8-3194-4a43-8f1b-8572e77dec04.png)

1.  The end-user’s gas payment to miners actually goes to an accrued account.
2.  (Optional) A system expense that some CML pay to receive faster state updates is the **state subscription fee.** If the miner has a state subscription, then the system will pull from the accrued balance whenever this fee is due. If there’s no funds in the accrued balance when the fee is due, then some of the CML’s bonding curve tokens will be liquidated to pay for the charge.
3.  If there’s funds in the accrued balance, then at regular intervals (currently anywhere between 5–20 minutes) they will be swept and paid out as dividends to CML bonding curve token holders.

More information is available in our [billing-faq](t-rust/obsidian/_gitbook-dev-docs/030_billing/billing-faq.md).