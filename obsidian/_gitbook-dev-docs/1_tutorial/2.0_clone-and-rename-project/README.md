## Tutorial and boilerplate

https://github.com/tearust/tutorial-v1 is the git hub repo of our tutorial. It can also be used as the boilerplate if you want to build a new TApp but doesn't need to start from scratch.

This repo has many branches. Every branch is a step in our tutorial. You can find the description for every branch in the README file.

The `master` branch and `login` branch are the most commonly used boilerplate. The `master` branch is hello world example, the `login` branch has almost all basic common features built-in. 

If you are going to verify if your development envionment is setup correctly, you can use the `master` branch and test the hello world. But if you are going to make a full featured TApp, the `login` branch might be a better choice, because it comes with the basic TApp features, such as "login/out", "transfer fund", "query balance". We believe most TApps would need those features, so we put them together into the utility library come with this `login` branch.

## Clone the tutorial master branch

Run `git clone https://github.com/tearust/tutorial-v1.git` to clone code to local.

If you would like to check in your modified code to your own git  repo, please rename the project name to avoid conflict.

## Front end and back end
There are two folders in the root of tutorial-v1 code repo.
- sample-actor: This is the back end lambda function. Similar to the backend web services in web2 world.
- sample-front-end: This is the front end code. Similar to the frontend SPA in web2 world.

In the future steps(branches) you will see a new folder called sample-txn-executor. That is the transaction handler function that runs inside the state machine. Similar to the stored procedure in database in web2 world.

