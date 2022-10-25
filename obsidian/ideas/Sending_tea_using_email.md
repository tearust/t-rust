# Goal
If user doesn't have a layer2 eth address, anyone can send TEA token(in the future any ERC20 token)to his email address. After that, if this email address has TEA balance, he can login using this email address. The state machine can generate a hash of his email as ERC20 address and store the TEA balance to his account. If he want to consume or transfer out his TEA token, he will need to run into OTP to authenticate. Then transfer his token to anyone else inside TEA ecosystem.

# Some send TEA to an email
Add a transfer to email button in Tapp Store Account page.
Input an email address. Regardless if this email is valid or already registered in TEA project before. There is actually no "registration of email" in TEA project. So , no varification is done here.

When processing this txn, the to_address is Hash(email) convert to H160. Note, there is no private key for this H160 address. So it is not a valid layer1 address. Any fund transfer to this account in layer1 will be LOST!!! To prevent the loss, we will not showing this H160 to the user UI all the time.

Once we have the H160 address. transfer would be the same as others. A typoe mov txn.

# Send email to receiver 's email

Because the hash to H160 is not reversable, we will have the receiver email in the txn params so that after a successful transfer, an email will be send using [[sendgrid_provider]]. 

The receiver should receive an email including

- Who send you the token. Address or another email(if the sender is also using email send)
- The amount of TEA token received
- Instruction on how to transfer this fund, the links. 
The user will need to click the link to use this fund, otherwise, the fund will be stay in his H160 account forever.

# Receiver check balance

Reciever click the link go to TApp Store app. Right now we only have metamask login, he cannot login, because he doesn't have the H160 address, even he has, there is no private key associated with it. We will need to add a new login using email button. See [[login_using_email]].

After login, a valid session key allow this user to view the balance as if he is a regular TEA user.

# Sending TEA using email and other actions

Additional OTP is needed if transfer a large amount of fund. The process is the same as [[login_using_email]]

There is nothing special for those email login user as long as they have the session_key after login. They works as the same as other users. 

# No topup and withdraw

Those email login user can work as most as the same as other metamask login user, but they cannot Withdraw. That is because there is no private key coresponding to this hash generated H160 address. If withdraw, the fund will be lost FOREVER!!! We should have logic to prevent this. For example, disable "Topup and Withdraw" in the login auth box.

# Sending fund to other ERC20 address and cash out

This is the same transfer operate as others. But this send_to address need to be a standard ERC20 address to withdraw cashout. Email generated address is not able to topup or withdraw.



