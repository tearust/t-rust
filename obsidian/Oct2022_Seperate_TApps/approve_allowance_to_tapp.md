Approve allowance to tapp will set an amount of max allowrance from one end user to a TApp for a certain length of time. The expire time is calculated from the time of approve.

During this period of time, the TApp can call cross_move to transfer at most that allowance TEA to this TApp's deposit account. 

After that period of time, the allowance is automatically revoke.

Once the TApp actually cross_move a certain amount of TEA from the allowance, the remaining will reduced. Once the remaining is zero, no more cross_move is allowed.

TAppStore should store this remaining number in statemachine. Both TApp and TAppStore can check this number.

# Storage
tokenid, approver_address, amount, expire_time

# Increase / decrease allowance
End user can add allowance at any time. When add
amount = amount + new_allowance // or - in case of decrease
expire_time = now + new_allowrance_time

# Revoke
User can revoke the allowance. Remove this record

# Operations

What fund transfer operations a TApp can request TAppStore

See [[TApp_fund_operations]]
