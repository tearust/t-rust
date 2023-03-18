In this tutorial, we we'll learn how to transfer funds.

The business workflow of this step would be:

* After owner creates a task, another person called "absent-minded" will try to work on this task. "Absent-minded" takes this task and clicks "complete" when done.
* The owner verifies that the task failed. So the owner "rejects" the task. 
* "Absent-minded" loses their deposit.
* The third user called "hard-worker" takes the same task and clicks "complete" when done.
* The owner verifies hard-worker's work is successfully done and clicks the "Confirm" button.
* The winner takes the price and the absent-minded's deposit.
* The task is done.

We'll learn in this walkthrough how to transfer funds between users based on business logic. 

## Build

As we've done several times in previous steps, we `git checkout reward` to the reward branch. Next build the sample-actor and sample-txn-executor two wasm file. They'll be copied to the dev-runner local/a-node and local/b-node folders. 

In order to clean up the state data from previous steps, make sure you delete the `.tokenstate` file first. This will make dev-runner start with a fresh state.

Start dev-runner by running `docker compose up` from the root of the dev-runner directory.

Go to sample-front-end and `npm start`. Go to http://localhost:3200.

Because this is a brand new state, please make sure you click the Init TApp token and Init TApp db buttons before anything else which we've explained the reason why previously.

## Test the business logic

Let's follow the designed business logic.

User A as owner creates a task. Next you'll want to log out of that owner account and switch to the second user (we call them "absent-minded") in your Metamask. If you haven't connected to this URL, please do so now. Then login as absent-minded. 

Because absent-minded is a new user, they have zero balance now. In order to continue, use "Faucet" add 1000 TEA to his account.

Then switch to the Task page where you'll find the UI has a "take" button as shown below:

![Pasted image 20230317091008.png](../../../Pasted%20image%2020230317091008.png)

This task is created by the owner, so as a worker, "absent-minded" can take this task.

After clicking "take", absent-minded is now the worker for this task. We'll simlulate this worker has done some work, then click "Complete".

![Pasted image 20230317091258.png](../../../Pasted%20image%2020230317091258.png)

At this time, user absent-minded's balance changes from 1000 to 995. 

![Pasted image 20230317091328.png](../../../Pasted%20image%2020230317091328.png)

That's because by taking this task, the user will need to pay 5 T as a deposit. In the next step, if their work was accepted by the owner, they would take the 5T deposit back as well as the 10T price reward. Well, let's see if they are lucky enough.

Now let's log out as absent-minded, and re-login with the Owner account.

We can see the task has two buttons: "Confirm" and "Reject".

![Pasted image 20230317091629.png](../../../Pasted%20image%2020230317091629.png)

In our demo, we assume that user "absent-minded" somehow mismanaged the task and that the owner isn't satisfied, so the owner clicks "Reject". Note, as a demo, we assume everyone is honest. We may get into a Secured Oracle Feature to make such decision out of human's control. But that would be in our future version and potentially in tutorial-v2.

After rejection, absent-minded lost their 5T deposit. The task status is set back to "new". Anyone can take this task and the winner will take 10+5=15T.

Now logout as the owner and switch to the third user account. This person's name is "hard-worker". Hard-worker will login, and also add 1000T test token using faucet. Follow the same steps to take the task, and click complete button. Again, log out as hard-worker, and login as owner.

This time, the owner is satisfied with the work of hard-worker. The owner thus will click "Confirm".

This task is "Done". To verify if the user "hard-worker" ahs received the 15T reward, you can logout of the owner account and log back in to hard-worker's account to check their balance. You should find 1015 T in their account balance.

![Pasted image 20230317092248.png](../../../Pasted%20image%2020230317092248.png)

So the workflow matches our expectation.

## No changes in sample-actor

From the `sql` step to `reward` step, there are no major changes for the sample-actor project. So we'll skip the sample-actor.

There are a few updates on the sample-front-end project, but all of them are standard javascripts front end code changes with not too much relationship to our funding transfer feature. We'll skip the detailed explanation, please go to the source code at `src/layer2/task.js` to read the code yourself.
