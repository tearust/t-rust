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

Run the dev-runner. `docker compose up`

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

TODO://