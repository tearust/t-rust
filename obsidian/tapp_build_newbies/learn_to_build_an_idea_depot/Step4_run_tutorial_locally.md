# Step 4: Run Idea Depot Locally

## Build the 'tapp_idea_vote' Branch
This is the step where you'll finally get to run the Idea Depot TApp (Idea Voter) locally on your machine. First you'll want to make sure you've followed the instructions in [installing the local development environment](https://dev.teaproject.org/020_tutorial/010_install-dev-env).

Once you've dowloaded the `dev-runner` repo, it's time to clone the Idea Depot's code repo to your local machine. Change directories to wherever you want to store the local copy of the repo and then run:

`git clone https://github.com/kevingzhang/tapp_idea_vote.git`

The first steps are to build the state-actor and actor wasm files. They'll be copied to the **dev-runner local/a-node** and **local/b-node** folders. 

The build scripts are in the following locations of the `tapp_idea_vote` repo:

- `state-actor/build.sh`
- `actor/build.sh`

You can now hen start the `dev-runner` environment locally by running `docker compose up` from the root of the dev-runner directory.

Before we start the front end, you'll want to use node version 14.14.0 for this tutorial. Make sure you have nvm 14.14.0 installed and set as default:

```
nvm install 14.14.0
nvm use 14.14.0
```
Now from the `front-end` directory of the `tapp_idea_vote` repo, run the following commands:

```
npm install
npm start
```

## Initialization steps in the browser
Now that the backend server is running, we'll need a few initialization steps before we can use the fund transfer function in our task TApp. The first step is to access the TApp in your browser by navigating to [http://localhost:3200](http://localhost:3200).

### 1. Init the TApp
Because this is a brand new state, please make sure you click the Init TApp token and Init TApp db buttons located in the **Admin** page before doing anything else.

<img width="1264" alt="Screenshot 2023-05-11 at 3 13 55 PM" src="https://github.com/kevingzhang/tapp_idea_vote/assets/86096370/5db98395-274f-4752-80e5-ff6d2533ef77">

### 2.  Login to TApp
Now you can login to the TApp by clicking the login button in the upper right of the browser to spawn the Metamask modal window to login to the app.

### 3. Use the faucet & set spending limit

Your account has no TEA when beginning from a fresh state, so you'll need to use the **Faucet** button to load your account with some TEA. Note that this sends 1000 T to your layer2 account, and you don't need to be logged in with Metamask (layer1) to do this step.

Next you'll need to set the spending limit for the TApp before using it. you can do this by clicking the **Set spending limit** button right next to the faucet button. Each TApp in the TEA Project has a spending limit that is set by the user to ensure that the TApp's business logic can withdraw user funds only up to that amount.

<img width="1270" alt="Screenshot 2023-05-11 at 3 27 58 PM" src="https://github.com/kevingzhang/tapp_idea_vote/assets/86096370/9f364718-f30d-4506-a473-dc08a8b9c70a">

Note that the last 2 steps (using the faucet and setting the spending limit) are only available in the local environment and these buttons won't exist in the real deployment of the app.

## Test the business logic

Now that we're ready to use the TApp, let's first go through the design of the business logic.

The Idea Voter TApp allows anyone to submit an idea for an app along with the amount of T they want to pledge to vote for it. All users (including the idea originator) can vote on the app idea from the **Idea list** by pressing the vote button which boosts the app idea by one vote while simultaneously deducting 1T from their balance. The idea list is ordered according to the popularity of the ideas (i.e. based on the number of votes it's received).

Anyone can create an idea for an app:

<img width="1263" alt="Screenshot 2023-05-11 at 3 28 14 PM" src="https://github.com/kevingzhang/tapp_idea_vote/assets/86096370/6ec0c6db-7098-477e-b347-6f5306898c3e">

They're required to fill out 3 pieces of information:

1. The title of their app idea
2. The number of T they want to seed for the initial pledge
3. A description of the app idea

Here's an example of the app idea form filled out:

<img width="996" alt="Screenshot 2023-05-11 at 5 06 54 PM" src="https://github.com/kevingzhang/tapp_idea_vote/assets/86096370/3808afe4-f6aa-4f34-b86e-2e37521cd565">

This app idea can now be voted on by anyone in the community:

<img width="1257" alt="Screenshot 2023-05-11 at 5 10 26 PM" src="https://github.com/kevingzhang/tapp_idea_vote/assets/86096370/12d00087-90d1-4e1d-b4c3-eb7edf856080">

