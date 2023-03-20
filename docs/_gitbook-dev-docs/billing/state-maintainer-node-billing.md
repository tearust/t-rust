# State Maintainer Billing

The [tapp-billing](t-rust/docs/_gitbook-dev-docs/billing/tapp-billing.md) shows the revenue side for the state maintainer nodes, namely the txn fees and memory tax. To show a complete picture of the profitably of being a state maintainer we should also describe the associated expenses of running a state maintainer node.

The following graphic describes the state maintainer revenue and expenses and their destinations:

![State Maintainer Billing](https://user-images.githubusercontent.com/86096370/218186861-63a9d980-dafa-45cf-96f5-6a015d942353.png)

1. The state maintainers pay a **Harberger Tax** on their self-assessed value to a **temp treasury fund**.
1. The temp treasury fund sweeps these funds at regular intervals to the public service rewards due to the CML nodes for performing public services like remote attestation.
1. Surpluses above what’s needed to pay for the public service rewards are remitted to the TEA DAO.

Readers interested in learning more about the structure of state machine payments can consult the following article: [Efficient State Machine Usage Through Taxation](https://teaproject.medium.com/proposal-efficient-state-machine-usage-through-taxation-2010ab1b294f).
