# Gas Fees

Tokenomics (Token-Economics) is an important design consideration to incentivize desirable behavior in a decentralized ecosystem by rewarding participants who provide useful services. The TEA Project has a carefully designed tokenomics. The following tutorial will cover some basic concepts of the TEA Project's carefully designed tokenomics. For full details, please see the tokenomics section of our white paper.

## Gas fee

When endusers want to use a TApp, that TApp is hosted by a decentralized "miner" in our TEA network. Because this miner is providing their hardware for the enduser's use, the miner should be paid for providing their machine and rewarded for their service. This is where  the **gas fee** comes into play, namely the enduser pays a gas fee to the miner for the computing hardware service they provide. The gas fee is calculated according to how much resources are used on the miner's host machine.

In the TEA Project's wasm runtime, we have a billing system that measures the CPU instruction when the CPU is executing the wasm actor code. The result of this measurement is summed up every few minutes. We currently set an exchange rate of 10M CPU units to 1 TEA. This exchange rate may change in the future as directed by the TEA DAO. At this moment, we can assume that it's a constant value, and that **the TEA's value is anchored to the computing consumption**. 

In this tutorial step, you'll see a new log page which includes the gas payment log. 

![Pasted image 20230321092915.png](../../../Pasted%20image%2020230321092915.png)

As you can see from the screenshot, most regular operations will cost between 0.00001 to 0.001 TEA. For some complicated computations, it may be as large as 0.1 TEA but this would be very rare. As a developer, you should always run the local testing in dev-runner and monitor this log page to get an estimate on how much gas your code may consume. There are many ways to optimize your code to consume less gas. Most rules developers used for Solidity (Ethereum) would apply to the TEA Project as well.

## Build and run

Assuming you're in the `reward` branch and continuing from our last tutorial, stop the docker container as well as npm by typing Ctrl C seperately in their respective terminal windows. We'll need to clear the stored state before moving on to the new branch.

In the `dev-runner` terminal window run `rm -rf .tokenstate` to clear the stored state. Once you've cleared the old state, when you start `docker compose up` next time, you'll need to reinitialize the token and database. This is an easily forgotten step so we want to emphasize it and will remind the reader later in the tutorial.

In the `tutorial-v1` terminal window, switch to new gas branch by running the following command: `git checkout gas`. 

Run `./build.sh` specifically in the `sample-actor` and `sample-txn-executor` folders. Verify the two actor wasm files are newly built in `dev-runner/local/a-actor` and `dev-runner/local/b-actor`. 

Now we can start the dev-runner by issuing `docker compose up`, then in `tutorial-v1/sample-front-end` run `npm install` and `npm start`. Nest go to the http://localhost:3200/ url in your browser.

## Test locally

Do not rush to login yet. Click the "Click here to send request" button in the Help page first. You should see an alert of Hello World. If not, the backend docker has not completed loading yet. Just wait another few minutes and try again. 

Because you've deleted `.tokenstate`, this is a brand new environment. You'll need to go to the Admin page and click the two buttons: Initialize token and Initalize database. Otherwise, all other operations will fail.

Now you can login. Then make sure you click "Faucet" to get 1000 test TEA. Otherwise, you won't have enough funds to run any operations or pay any gas fees.

You can run some test tasks as you did in last tutorial step. This is a helpful exercise as a way to consume some gas so such that it'll show up in the log after a few minutes.

Assuming you have done some operations such as creating a task or taking a task, you should be able to see a record of your actions in the Log page after a few minutes. The records will show your consumption and how much you paid for the gas fee.

Note that in the log page, you'll see all TEA transactions including gas fees as well as in-app purchases and fund transfers. 
