An additional button sit along side of login using Metamask.

Click to ask input email address. Also an OTP input box and send OTP.

We only allow the process this txn if the email has balance > 0. If there is no such email or balance == 0, we ignore this request. He would have to go to another [[faucet_process]] to get some TEA to start.

Backend use [[sendgrid_provider]] to send OTP to the email. Once receive, end user input the OTP and continue.

Backend verify OTP then generate a Session key so that this user can login and use as other regular users.

OTP has short live. We can set a 90 seconds life in storage. Then delete after expired. Also if the OTP is used, it is deleted.

Send OTP is not free, this email address user need to TEA on this. That is why we require this address has balance > 0.