
Tokenomics (Token-Economics) is the most important design to a full decentralized ecosystem. Every one in the ecosystem should play fair to get rewarded by providing services. TEA Project has a carefully designed tokenomics. In our tutorial we will cover some basic concepts, for full detail, please go to the TEA Project tokenomics white paper.

## Gas fee
 
TODO:// George, please add your definition of gas fee. Why we need gas fee, who pay gas fee to who?

In TEA Projecct wasm runtime, we have a billing system. It will measure the CPU instruction when the CPU exeucting the wasm actor code. The result of the measurement is sum up every few minutes. We currently set an exchange rate to 10M unit to 1 TEA. This exchange rate may change in the future by the DAO. At this moment, we can assume this is a constant value, and **the TEA's value is anchored to the computing consumption**. 

In this tutorial step, you will see a new log page. In this page, you will see the gas payment log. 
![[Pasted image 20230321092915.png]]
As you can see from the screenshot, in most time, the regular operation will cost 0.00001 to 0.001 range. For some complicated computation, it may be as large as 0.1 TEA but very rare. As a developer, you should always run the local testing in dev-runner, monitor this log page to get an estimate on how much gas your code may consume. There are many ways to optimize your code to consume less gas. Most rules developers used for Solidify would  apply to TEA Project as well.

## Build and run

Assuming you are in the `reward` branch from our last tutorial. Stop docker and npm by typing Ctrl C seperately in their terminal windows. We will need to clear the stored state before moving on to the new branch.

In dev-runner terminal window run `rm -rf .tokenstate` to clear all stored state. Once you clear the old state, when you start `docker compose up` next time, you will need to reinitialize the token and database. This is the most forgotten steps. We will repeat this again later.

In tutorial-v1 terminal window, switch to new gas branch by `git checkout gas`. 

Run `./build.sh` specifically in the `sample-actor` and `sample-txn-executor` folders. Verify the two actor wasm files are newly built in `dev-runner/local/a-actor` and `dev-runner/local/b-actor`. 

Now we can start the dev-runner by `docker compose up`, then in tutoria-v1/sample-front-end run `npm install` and `npm start`. Go to the http://localhost:3200/ in your browser.

## Test locally

Do not rush to login yet. Click the "Click here to send request" button in the Help page first. You should see an alert of Hello World. If not, the backend docker has not completed loading yet. Just wait another few minutes and try again. 

Because you have deleted `.tokenstate`. This is a brand new environment. You will need to go to the Admin page to click the two buttons: Initialize token and Initalize database. Otherwise, all other operation will fail.

Now you can login. Then make sure you click "Faucet" to get 1000 test TEA. Otherwise, you won't have enough fund to run any operation nor pay gas fee.

You can run some test tasks as you did in last tutorial step. Just want to consume some gas so that you will find the log after few minutes.

Assuming you have done some operations such as creating task, taking task etc. After few minutes, you can go to the Log page. You should see a list of logs. Those are you consumption and how much you pay for the gas fee.

Note, in the log page, you will see all banking transactions including gas fee and in-app purchase and fund transfer. 