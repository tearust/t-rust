# Time based auction
## Winner definition
If no one propose a valid new price within a fixed length of time (we use 2 hours), the current bidder wins

## What is "Valid" new price?
Rule 1: The new price will need to be higher than a time_based minimal price
Rule 2: The new price will need to be proposed within the fixed length of time (we use  2 hours) since last valid bid.
Rule 3: Bidder has paid deposit that can cover the cost of bidding

## What is "time_based minimal price"
Once a new valid bidding price is accepted, the next bidder has to bid at least at this new time_based minimal price. The new_based minimal is 1% higher than the current price since the beginning. After 15 minutes, it is increased to 2% higher than the current price. Every 15 minutes, there is a 1% increase. At the point of 1 hour 45 minutes after current valid bid, the time_based_minimal price is 7% higher. Once the time reaches the 2 hours point, the bid is over. the current bidder is the winner. The winner take this seed at the bidding price, not the time_based minimal price. The time_based minimal price is used to the next bidder.

## What is the parameters in the production?
The 0.1% increase and 2 hours are all parameters we use in the testnet. We will set different parameters for the mainnet when production.


# Benefit
Most of logic can be handled at the front end. It is very simple.
Back end only need to verify if the new proposal valid or not. 
There is no need for cron job.
There is no need to rush in the last second before the bidding window close.

# UI
A seed with all meta data, such as timespan, performance etc. Those are DNA.

Current bidding price: the last valid and accepted bidding price.
Current bidder address: If no one beat his price, he will be the winner 2 hours after his current bidding time.
Ending time: A human readable time. It is 2 hours from the current valid bidding is accepted.
Minimal price: This is the time_based minimal price it will change every 15 minutes.
The Bid button: Input your price and click "bid"
Claim the winner: If you are the winer, you click this button to claim. This is trigger the backend workflow to transfer the seeds ownership after validation.
