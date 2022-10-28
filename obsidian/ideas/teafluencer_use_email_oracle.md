
This is an example on how an app uses our email oracle for OTP.

Using this feature, anyone can invite others by sending some TEA token to their email address.

# Designed typical workflow
1. An influener use his social media to announce his followers to go to a "giveaway" tapp ([[giveaway_tapp]]). 
2. His followers can click the link go to the [[giveaway_tapp]] and input email address to receive free test TEA token. 
3. Our backend logic of [[giveaway_tapp]] calls our email oracle and [[sendgrid_provider]] to send OTP to the followers' email address.
4. Followers receive OTP email. In the mail, we explained how to continue to use TEA Project. The followers go to wallet.teaproject.org, click any node to go to tappstore. 
5. Login using his email which used to register and receive OTP. In the new email login page, he can input the OTP, his own email, influencer_eth_address. After backend verification, he can login as a regular user without topup/withdraw features.
6. If he (the follower) doesn't want to withdraw to layer1, there is no need to him to create a metamask account. He can do almost everything in TEA project. The only cumbersome is he will need to use OTP login everytime. This makes additional gas cost and waiting time for OTP. So we will remind the user everytime on how to not using email login with metamask. 
7. If he wants to switch to metamask account. Follow instruction to create a metamask account. Transfer all his assets to this new metamask account. Use this new metamask account for future uses. However, his old email account is still valid, just need to OTP everytime he use it.

# Product changes
- New [[sendgrid_provider]]
- New TApp , or a new tab inside TEAFluencer. Register email and receive test TEA token
- New actor that verify OTP, send OTP. This can be used by TeaFluencer or any other apps.
- New email login logic and UI for TAppstore. Any other TApp can use it too. Hopefully make it a public library that all apps can reuse
- New session_key logic that allow email users to be the same as other metamask users, without topup/withdraw features.
