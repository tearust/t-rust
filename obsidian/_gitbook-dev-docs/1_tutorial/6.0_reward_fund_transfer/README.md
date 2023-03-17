In this tutorial, we will learn how to transfer fund.

The business workflow of this step would be:
- After owner created a task, another guy called "loser" will try to work on this task. Loser takes this task and click "complete" when done.
- The owner verify the task failed. So the owner "reject" the task. 
- The loser losts his deposit.
- The third user called "Winner" takes the same task. And click "complete" when done.
- The owner verify winner's work successfully. Owner click "Confirm".
- The winner takes the price and the loser's deposit.
- The task is done.

We will learn how to transfer fund between users based on business logic. 

## Build

As we have done several time in previous step, we `git checkout reward` to the reward branch. Build the sample-actor and sample-txn-executor two wasm file. They will be copied to the dev-runner local/a-node and local/b-node folders. 

In order to clearn up the state data from previous step, make sure you delete the `.tokenstate` file first. This will make you start the new dev-runner fresh.

Start the dev-runner by running `docker compose up`.

Go to sample-front-end and `npm start`. Go to http://localhost:3200.

Because this is a brand new state, please make sure you click the Init TApp token and Init TApp db buttons before anything else. We explained the reason in previous step.

## Test the business logic

Let's follow the designed business logic.

User A as owner create a task. Log out. Switch to the second user (we call him loser) in your Metamask. If you have not connect to this URL, please do so now. Then login the loser. 

Because loser is a new user, he has zero balance now. In order to continue, use "Faucet" add 1000 TEA to his account.

Then switch to the Task page, you will find the UI has a "take" button as shown below:

![[Pasted image 20230317091008.png]]

This task is created by the owner, so as a worker, loser can take this task.

After click "take", the loser is now the worker of this task. We simlulate this work has done some work, then click "Complete".

![[Pasted image 20230317091258.png]]

At this time, the loser's balance changed from 1000 to 995. 

![[Pasted image 20230317091328.png]]
That is because by taking this task, he will need to pay 5 T as deposit. In next step, if his work was accepted by the owner, he will take the 5T deposit back and 10T price reward. Well, let's see if he is lucky enough.

Now lets log out Loser, re log in with Owner.

We can see the task has two button "Cofirm" and "Reject".

![[Pasted image 20230317091629.png]]

In our demo, we asume the Loser screwed up the task, the Owner is not satisfied, so he click "Reject". Note, as a demo, we assume everyone is honest. We may get into a Secured Oracle Feature to make such decision out of human's control. But that would be in our future version and potentially in tutorial-v2.

After rejection, the Loser lost his deposit 5T. The task status is back to "new". Anyone can take this task and the winner will take 10+5=15T.

Now logout the Owner, switch the third user account. His name is "Winner". Winner login, and also add 1000T test token using Faucet. Follow the same steps to take the task, and click complete button. Again, log out Winner, Login Owner.

This time, the Owner is satisfied of the Winner's job. He click "Confirm".

This task is "Done". To verify if the Winner received the 15T reward, you can logout Owner, and Log in Winner and check his balance. You will find 1015 T in his account balance.

![[Pasted image 20230317092248.png]]

So the workflow match our expectation.

## No changes in sample-actor

From `sql` step to `reward` step, there is no major changes for the sample-actor project. So we will skip the sample-actor.

There are a few updates on the sample-front-end project, but all of them are standard javascripts front end code change. Not too much relationship with our funding transfer feature. We will skip the detail explanation, please go to the source code at `src/layer2/task.js` and read the code youself.

