# Step 2: Using Existing Tutorial

## Leveraging the Existing Tutorials to Build our Idea Voting App
The best way to learn how to build a TApp is to read our dev tutorials. Developers can check out our [developer docs tutorial section](https://dev.teaproject.org/020_tutorial) to learn about coding a TApp. One problem we hear from developers new to web3 is that they feel burdened having to learn about smart contracts (and smart contract tooling), new programming languages, and blockchain concepts. The TEA Project abstracts all of that away. Even though the TApp will leverage blockchain and run decentralized, you as the developer can use any Wasm-compatible language and even have an SQL database available.

The tutorial goes over how to create a Task TApp where anyone can propose a task along with a bounty reward. Anyone who wants to work on the task will put up a good faith deposit and receive both their deposit and the bounty when they finish the task.

Our idea depot voting app meshes well with the existing tutorial as the idea voting and task apps have many features in common. Because of their similarities, we'll leverage the existing tutorial code base and make minimal changes. 

## Picking the Best Tutorial Starting Point
To build our idea depot voting TApp, we'll find the closest step in the tutorial as our starting point. We'll choose the following `Reward fund Transfer` branch as the closest starting point. You can find the link to that branch [here](https://dev.teaproject.org/020_tutorial/060_reward_fund_transfer).

We're choosing this branch for the following reasons:

- It has a fund transfer feature. We'll need this fund transfer functionality due to the deposit / collateral required when voting.
- It has basic SQL support which it inherits from the previous `SQL` step in the tutorial. We can use the basic SQL features and then add our own business logic on top.
- The Metamask login/out, account balance, and general main framework are all ready to use. These important features being available means that we don't need to build our own.

So we can use this branch as our code base. But we'll still add the following features that aren't existing in the tutorial so far:

- We'll create a new data structure called `idea`to hold the proposals.
- A SQL data structure to store idea content and voter address and deposit amount
- Modify the UI to shift away from task creater to idea creater. We'll also need to make the content area larger since the idea may need more explanatory content than the original task content.
- Modify the `worker` UI logic to `voter` UI logic. The "take" button changes to "vote". Additionally anyone can boost their vote, so we'll also need to allow users to add to their existing vote amount.
- Add backend logic to handle voter voting event and adding to the existing deposit 
- Add backend logic to calculate the total of all voters deposits added together to the creater's deposit. Use this total to sort in SQL.
- Remove the `worker` take-task and task-confirmation logic. In the original tutorial, a worker can take a task after which the task creator can confirm it's been done properly. After confirmation, the deposit would be released to the worker. We don't have any such logic for anyone to "take" a task (and thus for the original creator to confirm it) for now.

Once we have the above changes, the original tutorial will turn into a new TApp Idea Voting app.

In the next section, we'll start with the system design.
