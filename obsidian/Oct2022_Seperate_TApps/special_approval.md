When a TApp (rather than TAppStore itself) want to access to read / move end user's assets, this user need to approve this action in TAppStore. under TApp tab, action column.

The approve will including
- read
- move
- expence XX TEA with YY minutes  (**Tentative**) for easier first-time-user experience. [[#Time_based_approval]] below.

When the approval from the end user, when the tapp calls TAppStore B Actor for some query or mutation, it  will be rejected due to AuthKey invalid.

The approval will be saved in statemachine. So that the tApp and TAppStore can check if this user auth the operation at B actor without sending txns to A actor.

# Time_based_approval

For lower the time-time-user barrier. We can allow most tapps to have a minimal approval based on time. 
For example, the app can charge the user less than XX TEA every YY minutes.
In this case, even the tapp misbehavior, the loss is minimized.

The XX and YY can be a very low number, such as 0.001 TEA for every 10 minutes.

