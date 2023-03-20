## Tutorial and boilerplate

https://github.com/tearust/tutorial-v1 is the github repo of our tutorial. It can also be used as the boilerplate if you want to build a new TApp but don't want to start from scratch.

This repo has many branches with very branch a step in our tutorial. You can find the description for every branch in its associated README file.

The `master` branch and `login` branch are the most commonly used boilerplates. The `master` branch is a "hello world" example, and the `login` branch has almost all of the common basic features built-in. 

If you want to verify that your development envionment is setup correctly, you can use the `master` branch and test the hello world. But if you're going to make a full featured TApp, the `login` branch might be a better choice because it comes with the basic TApp features, such as "login/out", "transfer funds", and "query balance". We believe most TApps would use those features, so we put them together into the utility library that comes with this `login` branch.

## Clone the tutorial master branch

Run `git clone https://github.com/tearust/tutorial-v1.git` to clone code to local.

If you'd like to check in your modified code to your own git  repo, please rename the project to avoid conflict.

## Frontend and backend

There are two folders in the root of the **tutorial-v1** code repo:

* sample-actor: This is the back end lambda function that's similar to the backend web services in the web2 world.
* sample-front-end: This is the front end code that's similar to the frontend SPA in the web2 world.

In the future steps (branches) you'll see a new folder called sample-txn-executor. That's the transaction handler function that runs inside the [state machine](../z_glossary/state_machine.md). It's similar to the stored procedures of databases in the web2 world.
