## Features in this step

In this step, we will add the following features in to the first "hello-world" boilderplate:
- Login using Metamask
- Look up account balance
- Faucet to get some testing TEA token

These features are the most common features for almost every TApp (the Faucet only works in local developement, not on testnet or production). You may want to use this `login` branch as the boilerplate instead of the `master` branch because he `master` branch only has a "hello-world".

## Switch to the login branch and test run

Run `git checkout login` to switch to the login branch.

Now use what you have learnt in previous steps to build and load the code to the "dev-runner", see how it works.

Build the actor in sample-actor folder by running `./build.sh`.

Go to `dev-runner`, check the sample-actor.wasm is most recent 

```
total 9224
-rw-r--r--@ 1 kevinzhang  staff       85 Mar 10 12:29 README.md
-rwxr-xr-x@ 1 kevinzhang  staff  4118177 Mar 11 13:51 sample_actor.wasm
```

If the sample_actor.wasm is not recent, check if your build failed.

Run the dev-runner. `docker compose up`.  **Make sure you wait** about 2 minutes untill all actors are successfully activited. 

Now start the front end `npm start`

Then start your browser go to http://localhost:3200/

You should see the page looks like ![[Pasted image 20230311141039.png]]

Also you should notice the Metamask is open for you to connect.
![[Pasted image 20230311141155.png]]
If you did not see the Metamask popup, the most common reason is that you have not installed Metamask. Install Metamask before continue.

Please connect Metamask to this local address. 
![[Pasted image 20230311141251.png]]

> Q: Why do I need to connect Metamask?
> A:In Web3, there is no centralized account management system. That means you are the only person to control your account. No one else can disable, remove, alter your account. Metamask is a Ethereum browser wallet.  When you login, the Metamask is used to sign a txn (short for Transaction) using your own private key. This signed txn will be verified by the backend then the code will know it is you who is logging in. As long as you do not leak your private key, there is no way for anyone else impersonating you to login. In the future steps, all layer1 (blockchain) related txn will need Metamask to sign. 

After connecting Metamask to this URL, you can see your account and a login button showing at the right top corner.  Click the login button will bring up the Metamask again. This time, Metamask is used to sign the login txn. 
![[Pasted image 20230311141944.png]]

Please pay attension on the Message you are going to sign. In this case it is "sig". It is a placeholder for such a sample login. In other cases, this message has some real meaning, such as the authorization you give to the TApp. We will get more detail into this in the future steps. 

After login, the UI jumps to the account profile page and showing your balance.
![[Pasted image 20230312100636.png]]

You can see your have zero balance in your account. That is because everytime you start the dev-runner, the state will be reset to the init state. 

You can click the Faucet button to get 1000 *FREE* test token in your account. 

These are all the features we added to the `login` branch.

## What happened under the hood?

After we build the sample-actor, a new sample-actor.wasm is copied to the dev-runner local/b-node folder. When we launch the dev-runner, the actor will be loaded into he TEA runtime and activated. You should see this in the log.
`client-bob     | [enclave] INFO host_main{enabled_clients=["adapter", "libp2p", "third-api", "http"] app_id=0 tea_id="0000000000000000000000000000000000000000000000000000000000000000" conn_id="12D3KooWPLns5y2qhcLFb6WCYhTiT7gXnx9ubfUhcgNV8rdjCfee"}:  INFO activate sample actor successfully`

This means the sample-actor is loaded and active. It is waiting for request from client.

Then we started the front end. Click the login button on the web page. The front end generate a login message asking you sign using Metamask. The signed message and login request is sent to the backend sample-actor to complete the login process. Once loggedin, the front end will receive a auth_key similar to session key in web2. It is the proof of login user and will be attached for almost every future request. Once login is complete, front end jump to the account profit page. 

In account profile page sends a query request to sample-actor for the account balance. After a few seconds, the response attached the balance information is received by front end, and show in the UI. Yes, you can see 0 in the balance because it is a new test state. 

When you click the "Faucet" button, a transfer request is sent to the backend. In the local dev-runner mode, a special logic allow anyone can transfer 1000 TEA from the DAO_RESERVE account for testing purpose. That is why you can get 1000 FREE TEA. Note, this won't happen in non local dev-runner configuration. So you won't have such free gift in Testnet or Producction for sure.

Once the Faucet transfer completed, you can see the new balance 1000 TEA after a follow up query to balance. 

These steps are what happened just now. 

In next article, we will walkthrough the source code, you can clearly see how the logic works in the code. Stay tune.

