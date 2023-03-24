# Reward Fund Transfer

## Overview

In this tutorial, we we'll learn how to transfer funds.

This step continues to use the decentralized task app that was described in the previous `sql` branch of the tutorial. We're going to use a specific  business workflow for this branch of the tutorial as follows:

* After owner creates a task, another person called "absent-minded" will try to work on this task. "Absent-minded" takes this task and clicks "complete" when done.
* The owner verifies that the worker hasn't completed the task to specification. So the owner "rejects" the task. 
* "Absent-minded" loses their deposit.
* The third user called "hard-worker" takes the same task and clicks "complete" when done.
* The owner verifies hard-worker's work is successfully done and clicks the "Confirm" button.
* The winner takes the price and the absent-minded's deposit.
* The task is done.

We'll learn in this walkthrough how to transfer funds between users of this task app based on the business logic. 

## Build the 'reward' branch

As we've done several times in previous steps, we switch branches by running `git checkout reward` to the reward branch. Next build the two sample-actor and sample-txn-executor wasm files. They'll be copied to the **dev-runner local/a-node** and **local/b-node** folders. 

The build scripts are in the following locations of the `tutorial-v1` repo:

* `sample-actor/build.sh`
* `sample-txn-executor/build.sh`

In order to clean up the [state](../../z_glossary/state.md)  data from previous steps, make sure you delete the `.tokenstate` directory in the `dev-runner` repo before launching the docker container. This will make dev-runner start with a fresh state.

Start dev-runner by running `docker compose up` from the root of the dev-runner directory.

Now from the `sample-front-end` directory of the `tutorial-v1` repo, run `npm start`. 

## Initialization steps in the browser

Now that the backend server is running, we'll need a few initialization steps before we can use the fund transfer function in our task TApp. The first step is to access the TApp in your browser by navigating to [http://localhost:3200](http://localhost:3200).

1. Because this is a brand new state, please make sure you click the Init TApp token and Init TApp db buttons located in the **Admin** page before doing anything else.

<img width="1133" alt="Screenshot 2023-03-24 at 10 59 43 AM" src="https://user-images.githubusercontent.com/86096370/227608431-89da24e9-03d6-4e91-a28e-e14f63d02952.png">

2. Now you can login to the TApp by clicking the login button in the upper right of the browser to spawn the Metamask modal window to login to the app.

2. Your account has no TEA when beginning from a fresh state, so you'll need to use the **Faucet** button to load your account with some TEA. Note that this sends 1000 T to your layer2 account, and you don't need to be logged in with Metamask (layer1) to do this step.

<img width="1127" alt="Screenshot 2023-03-24 at 11 01 55 AM" src="https://user-images.githubusercontent.com/86096370/227608440-e8c2de65-149a-4de1-8051-f19028f7551b.png">

4. Next you'll need to set the spending limit for the TApp before using it. Each TApp in the TEA Project has a spending limit that is set by the user to ensure that the TApp business logic can withdraw user funds only up to that amount.

<img width="1143" alt="Screenshot 2023-03-24 at 11 01 03 AM" src="https://user-images.githubusercontent.com/86096370/227608436-80601f38-e2a4-4211-b21c-677d8e782265.png">

## Test the business logic

Now that we're ready to use the TApp, let's first go through the design of the business logic.

**User A** as the owner creates a task. You'll be playing the role of the task creator and of two different task workers for this section of the tutorial, which means you'll need to have three different Metamask accounts. After using the faucet and setting the spending limit for the owner account, you can now log out of the owner account and switch to the second user (we call them "absent-minded") in your Metamask. 

Because absent-minded is a new user, they have zero balance now. In order to continue, use "Faucet" add 1000 TEA to their account and also set the spending limit for the app.

Then switch to the Task page where you'll find the UI has a "take" button as shown below:

![Pasted image 20230317091008.png](../../../Pasted%20image%2020230317091008.png)

This task is created by the owner, so as a worker, "absent-minded" can take this task.

After clicking "take", absent-minded is now the worker for this task. We'll simulate that this worker has done some work, so go ahead and click "Complete".

![Pasted image 20230317091258.png](../../../Pasted%20image%2020230317091258.png)

At this time, user absent-minded's balance changes from 1000 to 995. 

![Pasted image 20230317091328.png](../../../Pasted%20image%2020230317091328.png)

That's because by taking this task, the user will need to pay 5 T as a deposit. In the next step, if their work was accepted by the owner, they would take the 5T deposit back as well as the 10T task reward. 

Now let's log out as absent-minded, and re-login with the Owner account.

We can see the task has two buttons: "Confirm" and "Reject".

![Pasted image 20230317091629.png](../../../Pasted%20image%2020230317091629.png)

In our demo, we assume that user "absent-minded" somehow mismanaged the task and that the owner isn't satisfied, so you as the owner will go ahead and click "Reject". Note, as a demo, we assume everyone is honest. We may release a **secure oracle** feature to take such decisions out of human control, but that would potentially be in a future version of this tutorial.

After rejection, absent-minded lost their 5T deposit. The task status is set back to "new". Anyone can take this task and the winner will take 10+5=15T.

Now logout as the owner and switch to the third user account. This person's name is "hard-worker". Hard-worker will login, and also add 1000T test token using the faucet as well as setting the spending limit for the app. Again follow the same steps to take the task, and click the complete button. Again, log out as hard-worker, and login as owner.

This time, the owner is satisfied with the work of hard-worker. You as the owner will thus click "Confirm".

This task is "Done". To verify if the user "hard-worker" has received the 15T reward, you can logout of the owner account and log back in to hard-worker's account to check their balance. You should find roughly 1015 T in their account balance (1015T less any small gas fees).

![Pasted image 20230317092248.png](../../../Pasted%20image%2020230317092248.png)

So the workflow matches our expectation.

## No changes in sample-actor

There are no major changes for the **sample-actor** project when we went from the `sql` step to `reward` step. There are a few updates on the **sample-front-end** project, but all of them are standard javascripts front end code changes with not too much relationship to our funding transfer feature. We'll skip the detailed explanation, please go to the source code at `src/layer2/task.js` to read the code yourself.
