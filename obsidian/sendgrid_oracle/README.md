# Workflow

Step1: APi register
Step2: User call API to send request to Gmail for OTP
Step3: User send OTP to host nodes
Step4: Host nodes send layer1 txn to confirm

# API_Register
## Goal
Before an App can use this Sendgrid service, this app need to register.
## Input
API_user_address: the id of the client (we call the app developer client). It is the address that pays all the cost. this address should have TEA balance enough to pay
sendgrid_api_key: This is stored in state, afterwards all sendgrid call will need this key.
## Output
API_key: This key is only known by both TEA Project and the API_user(the dev who make the app)
## Logic
If the API_user already in use, return err.
Generate random api_key
Store the API_user_address and generated api_key to statemachine.
Return API_key
## Cost
This API has standard gas
This API has a storage cost for the single api_key pair. Very little

# API_Unregister
## Goal
Remove the API_user_address ->API_key storage from state
## Input
API_user_address:
API_key: used to idendity the caller is the API_user
## Output
Ok()
## Logic
Verify the caller id
Delete the storage kvp
## Cost
This api call has standard gas
Once the key is deleted, no more storage cost requried

# Req_send_grid
## Goal
Customer send req from browser or mobile app. Ask the host to generate OTP and send to the given gmail account. 
## Input
customer_email_address:
API_user_address: this is the app dev registered API_user_address above
hash_apî_key_and_user_email: This is hash(API_key + customer_email_address). We use this to verify API_user_address
## Output
Ok()
## Logic
Verify hash_api_key_and_user_email by retrieve the api_key from key=API_user_address, then hash with the customer_email_address.
Generate a random OTP. Using the hardware trun random generator
Retrieve the sendgrid_api_key from the storage, use key=API_user_address
Use sendgrid_api_key to call SendGrid provider to send email to customer_email_address.
Save the random OTP to statemachine, under key=API_user_address/customer_email_address
Return Ok
## Cost
This api has standard gas fee.
This api has sendgrid cost(Due to the network traffic). This is not the fee charged by Sendgrid, because the API_user_address has paid to Sendgrid to make the sendgrid_api_key valid. This is out of TEA project.
## Notes
The App developer need to calculate the hash_api_key_and_user_email when user request. This can be generated at runtime and save to browser or mobile app. For every customer, this hash is a fixed value. it won't change as long as the developer does not unregister and regiest again.
This is a hash, so it is safe to send over http and store in web browser. Because everyone will have his own unique hash.

# Req_verify_OTP
## Goal
The customer send the OTP to host. If the host verify OTP correctly, the host will sign a txn to smart contract.
## Input
customer_email_address:
hash_api_key_and_user_email:
OTP:
send_to_smart_contract_address:
txn_params:
## Output
Ok()
## Logic
Verify the hash_api_key_and_user_email, this is the same as previous Req_send_grid.
Retrieve OTP from state under key=API_user_address/customer_email_address.
Verify OTP matches
Sign txn as requested in the input. This txn should be a multisig. If more than threshold maintainer signs, it should pass.
## Cost
This api has standard gas fee.
This api has statemachine signature gas fee, as txn_exec_fee.
This api has layer1 txn sign fee.
## Notes
The app has to deploy the smart contract. This smart contract knows the list of TEA Project maintainers addresses. As long as more than a threshold maintainer sign the txn, it is valid. then do whatever the smartcontract supposed to do. In this case, approve this user to be valid.



