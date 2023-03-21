The tea sdk utills wraps a bunch of utilities that the developer will need to use to interact with the TEA system. The source code is at [https://github.com/tearust/sdk/blob/master/utils/wasm-actor-utils/src/client/types.rs](https://github.com/tearust/sdk/blob/master/utils/wasm-actor-utils/src/client/types.rs)

Here's the list of functions:

- login: Used for user login
- query_session_key: to use the logged in user's session_key. This session_key is needed almost every time in order to contact the backend as the proof of user login.
- query_result: To get the response from previous requests. This is because all requests to the backend will be handled async.
- queryHashResult: TODO://
- logout: logout
- query_balance: query the account balance.
- query_deposit: query the deposit balance.
- query_asset:TODO://
- query_allowance:
- query_tapp_metadata: Get TApp metadata.
- query_err_log: get error log.
- query_system_version: get system version.

Some of the account concepts might be unfamiliar to you, such as "balance, deposit, allowance". Please read the TEA Project tokenomics for more details.

