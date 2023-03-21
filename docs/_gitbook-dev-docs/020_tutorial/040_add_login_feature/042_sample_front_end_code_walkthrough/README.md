# Sample Front-end Walkthrough - Login Branch

The sample-front-end has some major code changes from the `master` branch. Don't worry, this should be the only major change for the rest of the steps. The reason we've introduced these major changes is because we have put all commonly used utilities into this branch. In the future steps, major changes will be hapenning in the sample-actor backend. 

This `login` branch is probably the best boilerplate that you can use to build your own TApp from because it has all major commonly used utiility features, such as login, fund transfer etc.

## Code structure

## Handle user login

When user click Login button, the UI shows the LoginModel box. The login box VUE code is in views/modals/LoginModal.vue. Please pay attention on the data:

````
data(){
    return {
      loading: true,
      form: {
        
      },
      
      read: false,
      withdraw: false,
      consume: true,
      move: false,
      bonding_curve: false,
    };
  },
````

The `read, withdraw, consume, move, bonding_curve` are authorities that the user need to confirm. We explained in the previous article. If you want the end user to select any authority by default, you can set it to "true". Like the `consume` in our case.

The js code to handle use login is in `src/layer2/user.js`

````

  async login(self, permission_str) {
    const address = self.layer1_account.address;

    const chain = await self.wf.layer1.getChain();
    if(chain.name === 'Offline'){
      throw('You did not install metamask wallet, please login with your email address.');
    }


    // thanks for https://github.com/polkadot-js/extension/issues/827
    const data = permission_str;
    console.log('permission_str => ' + permission_str);

    try {
      const layer1_instance = self.wf.getLayer1Instance();
      let [sig, pk, msg_bytes, msg] = await layer1_instance.signMessage(data);


      sig = sig.replace(/^0x/, '');
      let rs = await txn.txn_request('login', {
        tappIdB64: base.getTappId(),
        address,
        pk: utils.uint8array_to_base64(hexToU8a(pk)),
        data: msg,
        signature: sig,
      });
      rs = await txn.query_request('query_session_key', {
        tappIdB64: base.getTappId(),
        address,
      });

      if (rs.auth_key) {
        const user = {
          address,
          isLogin: true,
          session_key: rs.auth_key,
          expird_time: Date.now() + 1800 * 1000,
        };

        utils.cache.put(F.getUserId(address), user);
        await store.dispatch('init_user');

        base.top_log(null);

        self.$root.goPath('/account_profile');
        return true;
      }

    } catch (e) {
      // TODO handle error.
      throw e;
    }
  },

````

We first check if the chain name is available from the Metamask SDK. If it doesn't exist, we display an error and ask the user to install Metamask.

````
const chain = await self.wf.layer1.getChain();
    if(chain.name === 'Offline'){
      throw('You did not install metamask wallet, please login with your email address.');
    }
````

Then we generate a sig of the msg `permission_str` from Metamask.

````
const layer1_instance = self.wf.getLayer1Instance();
      let [sig, pk, msg_bytes, msg] = await layer1_instance.signMessage(data);


      sig = sig.replace(/^0x/, '');
````

This is how Metamask asks you to sign.

Once the signed data is received from Metamask, the next step would be sending a "login" request to the backend. The code looks like this:

````
     let rs = await txn.txn_request('login', {
        tappIdB64: base.getTappId(),
        address,
        pk: utils.uint8array_to_base64(hexToU8a(pk)),
        data: msg,
        signature: sig,
      });
````

`txn.txn_request` is widely used all over the frontend code whenever we want to send requests to the backend.  Note the backend handle is always **async** so you shouldn't expect to get a response immediately. You'll always need to query the result using `await txn.query_request` as in the code below:

````
     rs = await txn.query_request('query_session_key', {
        tappIdB64: base.getTappId(),
        address,
      });
````

Once the response is received, the `auth_key` will be saved to the local cache. This auth_key is very important, we'll need to attach this key to all future requests to the backend so that the backend will know the user has been logged in. If the auth_key is either missing or expired, the user will be requested to login again. This is usually called "session time out" in the web2 world.

## Query account balance

Query account balance code is located in `src/layer2/user.js`

````
async query_balance(self, target = null, target_tapp = null,) {
    const session_key = F.checkLogin(self);

    const opts = {
      address: self.layer1_account.address,
      tappIdB64: base.getTappId(),
      authB64: session_key,
    };
    if (target) {
      opts.target = target;
      opts.targetTappIdB64 = target_tapp;
    }

    try {
      const rs = await txn.query_request('query_balance', opts);
      if (!rs.balance) {
        rs.balance = 0;
      }

      return rs ? utils.layer1.balanceToAmount(rs.balance) : null;
    } catch (e) {
      self.$root.showError(e);

      return 0;
    }

  },
  
````

First we'll need to get the login session key (the `auth_key` in the login session). `const session_key = F.checkLogin(self);` This session_key will need to be attached to the request. 

`opt` is the query_balance request parameter. We we'll need to tell the backend the following query parameters:

* Which app am I querying. Every app has a different account system.
* Which address (account) am I querying.
* Session_key. Used to verify that this user has logged in and has the right to query.

Then call query_request to query.

\`const rs = await txn.query_request('query_balance', opts);

The function `utils.layer1.balanceToAmount(rs.balance)` is used to convert the number to human readable text.
