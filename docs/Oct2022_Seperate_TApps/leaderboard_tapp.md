# Leaderboard tapp is free to use

Leaderboard is designed to be a free tapp. User doesn't need to pay anything to view the board. We use DAO RESERVE to pay for the txns cost and memory tax.

Most of the function of leaderboard is "read", Register is probably the only transaction.

# Login

Regualr login without auth key.

# Register

Register means the end user allow to show its financial data to the public inside the leaderboard app.

Without registerring, tappstore will not allow to show the balance to public.

Register is actually happen inside tappstore, because we cannot let any tapp to claim that it has the right to read any users data. The end user need to explicitly approve [special_approval](special_approval.md) another tapp (in this case leaderboard) to read my assets. Without such special approval, the normal TApp can only query TAppStore for [check_tapp_allowance](check_tapp_allowance.md) without checking assets.

In leaderboard, if user click Register button, it will show an instruction, tell him to do this in the [special_approval](special_approval.md) inside TApp tab inside TAppStore app.

So the register logic backend is still inside tappstore.

# Account component

# Account component

This account component can be show at the header.
It shows the current maintainer tapp deposit balance of this user.
A [refund](harberger_auction_tapp.md#refund)refund button to refund the existing deposit back to tappstore. User will be promopt to input amount of TEA to refund. But [deposit](approve_allowance_to_tapp.md) need to be done inside tappstore.


This can be reused as it shared to multiple tapps.

# Leader table

This is the typical leaderboard table.

# Mutations

There is no action in Leaderboard. it is typical a "read only" tapp.

# Queries

Leaderboard front end send query to **tappstore** B actor directly. 
The tappstore B actor check the end user id to make sure he has been registered to leaderboard via the TAppStore [special_approval](special_approval.md). If yes, it allow query the data back to the leaderboard front end. If not, reject the request.

We probably do not really need a B actor of Leaderboard.
