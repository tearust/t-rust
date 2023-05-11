# Step 4: Run Idea Depot Locally

## Build the 'tapp_idea_vote' Branch
This is the step where you'll finally get to run the Idea Depot TApp locally on your machine. First you'll want to make sure you've followed the instructions in [installing the local development environment](https://dev.teaproject.org/020_tutorial/010_install-dev-env) and then starting the `dev-runner` environment locally by running `docker compose up` from the root of the dev-runner directory.

Once your `dev-runner` environment is up and running, it's time to clone the Idea Depot's code repo to your local machine. Change directories to wherever you want to store the local copy of the repo and then run:

`git clone https://github.com/kevingzhang/tapp_idea_vote.git`

The first steps are to build the two sample-actor and sample-txn-executor wasm files. They'll be copied to the **dev-runner local/a-node** and **local/b-node** folders. 

The build scripts are in the following locations of the `tapp_idea_vote` repo:

- `sample-actor/build.sh`
- `sample-txn-executor/build.sh`

Now from the `sample-front-end` directory of the `tapp_idea_vote` repo, run the following commands:

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

TODO