# Retweet Task

## Retweet Task App
In this tutorial, we'll tweak our existing Task TApp such that the only kind of task available is to retweet (quote tweet) a tweet. Task creators will assign this task for others to quote tweet their desired tweet in an effort to make it go viral. This could be useful for developers who want their tweets seen by more people as quote retweets will show up in the other user's Twitter timeline.

An important difference with the previous version is that the reward no longer requires human intervention to release the funds. The task worker enters their quote tweet url which can be checked by API if it's in fact a quote tweet. If it passes the api check, then the funds are automatically released to the task taker.

## Business Workflow of Retweet Task
The business workflow for this branch is as follows:

- The task creator creates a task by entering their desired tweet they want quote retweeted. In addition, they also add in a reward amount for successful task completion and a deposit they require from the task taker.
- The task taker must quote retweet the source tweet and enter the url of their quote retweet in the task taker's modal window.
- Clicking confirm will send an api request to Twitter and confirm whether the entered url is in fact a quote retweet of the source tweet url.
- If the api confirms success, then the task taker gets the bounty as well their initial deposit back.
- If the api confirms that the quote tweet isn't of the original tweet, then the task taker's bounty is slashed and is added to the bounty for the task. The task then goes back into the available task queue and is available for anyone to take.


## Build the 'http' Branch

As we've done several times in previous steps, we switch branches by running `git checkout http` to switch to the **http** branch. Next we'll build the two sample-actor and sample-txn-executor wasm files. They'll be copied to the **dev-runner local/a-node** and **local/b-node** folders. 

The build scripts are in the following locations of the `tutorial-v1` repo:

- `sample-actor/build.sh`
- `sample-txn-executor/build.sh`

Start dev-runner by running `docker compose up` from the root of the dev-runner directory.

Now from the `sample-front-end` directory of the `tutorial-v1` repo, run the following commands:

```
npm install
npm start
```

## Initialization steps in the browser
Now that the backend server is running, we'll need a few initialization steps before we can use the fund transfer function in our task TApp. The first step is to access the TApp in your browser by navigating to [http://localhost:3200](http://localhost:3200).

### 1. Init the TApp
Because this is a brand new state, please make sure you click the Init TApp token and Init TApp db buttons located in the **Admin** page before doing anything else.

![](https://user-images.githubusercontent.com/86096370/227608431-89da24e9-03d6-4e91-a28e-e14f63d02952.png)


### 2.  Login to TApp
Now you can login to the TApp by clicking the login button in the upper right of the browser to spawn the Metamask modal window to login to the app.

### 3. Use the faucet

Your account has no TEA when beginning from a fresh state, so you'll need to use the **Faucet** button to load your account with some TEA. Note that this sends 1000 T to your layer2 account, and you don't need to be logged in with Metamask (layer1) to do this step.

![](https://user-images.githubusercontent.com/86096370/227608440-e8c2de65-149a-4de1-8051-f19028f7551b.png)


### 4. Set spending limit
Next you'll need to set the spending limit for the TApp before using it. Each TApp in the TEA Project has a spending limit that is set by the user to ensure that the TApp business logic can withdraw user funds only up to that amount.

![](https://user-images.githubusercontent.com/86096370/227608436-80601f38-e2a4-4211-b21c-677d8e782265.png)

Note that the last 2 steps (using the faucet and setting the spending limit) are only available in the local environment and these buttons won't exist in the real deployment of the app.

## Test the business logic

Now that we're ready to use the TApp, let's first go through the design of the business logic.

We'll focus on two different users to see the Retweet Task's busines logic: the **task creator** and the **task taker**. These will correspond to two different Metamask accounts. We assume that both accounts have used the faucet to get the initial 1000T in their respective accounts and set the spending limit for the app so they can use it for fund transfer.

### 1. Task Creator Creates the Task
The task creator creates the task by setting these three parameters within the task creation modal window:

1. Tweet url to be quote tweeted.
2. The deposit task taker must put up to take on this task.
3. The reward for completing the task.

(TODO: missing UI pic)

### 2. Task Taker Takes the Task

The Task page UI for the Retweet Task TApp has a "take" button as shown below:

![[Pasted image 20230317091008.png]]

Note that at the time of taking the task, the task taker's balance will decrease by the amount required by the task. If for example the deposit required is 5T, then the task taker's account balance will decrease from 1000 to 995. 

![[Pasted image 20230317091328.png]]

After clicking "take", our **task taker** account is now the worker for this task. After taking this task, a new modal will open up:

(TODO: missing UI pic)

To complete this task, the user will have to follow the instructions in the modal and complete a **quote tweet** of the original tweet. 

Once the task taker has pasted in their quote tweet url in the box, they'll click the **Confirm** button.

At this point the business logic branches according to the response from the Twitter api:

- If the api confirms it's a successful quote tweet, then the task taker gets the bounty as well their initial deposit back.
- If the api returns that the quote tweet isn't of the original tweet, then the task taker's bounty is slashed and is added to the bounty for the task. The task then goes back into the available task queue and is available for anyone to take.

Because the user testing their TApp has both accounts in their Metamask, switching between them will show the funds transfer logic. The task taker's deposit as well as the task creator's bounty are held in a virtual escrow account and divvied up according to if the task was completed successfully. Funds transfer can be confirmed by switching between the two accounts.

## TEA Project's Secure Oracle Feature
When we make the call to Twitter to get the api result of the retweet check, we're relying on Twitter to provide an oracle service which gets bridged over to web3 through our nodes. Note that TEA is able to vouchsafe the integrity of the oracle result because of our use of secure hardware. Once the oracle (the Twitter api in this example) reports its result to one of our TEA nodes, the result is stored within the protected enclave of the node. It's completely trustable as nothing can breach the integrity of the enclave, which is why we refer to it as a secure oracle.

The biggest oracle provider in the web3 space is Chainlink and their operating procedure is must different. They instead have their own consensus protocol where multiple Chainlink nodes have to reach a consensus on an oracle result before being able to vouch for its integrity. The TEA Project's use of secure hardware means that we don't have to engage in this wasteful consensus process just to get a trustable oracle result. And because TEA's secure enclaves are also the basis of its secure compute layer, conceivably any compute logic output done on the TEA node network is a trustable oracle result avaiable to be consumed by another endpoint.
