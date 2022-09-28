# Transfer buyer seat price to seller and deposit
## Check if payer has enough fund
If our local cache state feature is ready, in B Actor of Harberger Auction. Before sending buy_seat txn after user click "buy seat", first check the buyer has enough fund to pay. Check buyer's [[check_tapp_allowance]]   If allowance  < price + MIN_SEAT_DEPOSIT, prompt user to go to TAppStore to [[approve_allowance_to_tapp#Increase decrease allowance | increase allowance]] to reach the limit. 
else, send request to TAppStore A Actor Buy_Seat

In the TAppStore Actor BuySeat txn handler, Also need to check allowance  again as the B actor did. We check because the time different between B actor check and now.
## Pay MIN_SEAT_DEPOSIT
Run [[TApp_fund_operations#Deposit_from_tappstore]] , 
- token_id is Harberger Auction tokenid
- deposit_amount = MIN_SEAT_DEPOSIT - current_deposit. 
- user_address = buyer_address
So that the deposit is large enough to cover the MIN_SEAT_DEPOSIT.

## Pay original owner, the seller
Run [[TApp_fund_operations#payment_from_tappstore]] to pay the buyer seat price
- amount is price. 
- sender is buyer, the new owner.
- receiver is seller, the original owner

## Refund seller

Check remaining deposit for original owner. 
assert the remaining deposit > sql_query_deposit_amount. I do not know why it may happen, so assert.
Run [[TApp_fund_operations#Refund]] 
- refund amount sql_query_deposit
- user_address: original owner, the seller

The above 3 transfer is inside a single commit. So it will success or fail together. Do NOT put them into two seperate commit. They are a single transaction.


# Update SQL
The other follow up operation would be the same as before, update SQL, set Seat Pending status etc.
