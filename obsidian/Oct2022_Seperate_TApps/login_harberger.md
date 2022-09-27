In user login page of Harberger auction app. there should be a EULA. the EULA can be a placeholder at this moment. The EULA basically tell the user we will public release the ownership of seat, address, price etc. It is simply a privacy disclaimer. 

We will have read and write checkbox. Read is default selected and no way to deselect. The write is default selected by is able to deselect. If user want to have a read-only session, he can explicitly uncheck the "write" checkbox. In this case, the AuthKey will not have write option.

We do not need other checkbox, such as mov, withdraw. Those are not needed for all other TApps except TAppStore.. Because only TAppStore can topup or withdraw.

The login logic is the same as before. User use Metamask to sign a string of data structure. This is authkey. 

By decoding the authkey, we will know 
- Who signed, the user address
- Which app, the app_id
- Read, Write check. if it is ready-only, or read-and-write
- Signed time, and calculated expire time.