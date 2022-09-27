When a TApp (rather than TAppStore itself) want to access to read / move end user's assets, this user need to approve this action in TAppStore. under TApp tab, action column.

The approve will including
- read and show assets data to public
-  (**Tentative**) auto renew allowance. XX TEA with YY minutes. See[[#Time_based_approval]] below.

When the approval from the end user, when the tapp calls TAppStore B Actor for some query or mutation, it  will be rejected due to AuthKey invalid.

Without the read and show assets data to public ([[leaderboard_tapp]] would be one of the tapp requires this), the AppStore will only allow [[check_tapp_allowance]], not other assets.

# Time_based_approval

For lower the time-time-user barrier. We can allow most tapps to have a minimal approval based on time. 
For example, the app can charge the user less than XX TEA every YY minutes.
In this case, even the tapp misbehavior, the loss is minimized.

The XX and YY can be a very low number, such as 0.001 TEA for every 10 minutes.

