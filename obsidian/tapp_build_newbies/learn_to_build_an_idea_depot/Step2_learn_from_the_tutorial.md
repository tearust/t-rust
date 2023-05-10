The best way to learn TApp building is to read the tutorial !!! Please add linik here!!!

!!!Explain tutorial briefly !!!

Compare with the existing tutorial and the tapp_idea_vote we are trying to build, there are many features in common. So that we can leverage the existing tutorial code base and make minimal changes. 

We can find the closest step in the tutorial as our starting point. I found the step `Reward fun Transfer` ` might be the closest one. The reasons are:
- It has fund transferring feature. We will need the fund transfer due to the deposit when voting
- It has basic SQL support since the last step. We can use the basic SQL, then add our own business logic.
- The login/out, Account balance, and main framework are ready to use. We do not need to build our own.

So we can use this branch as our code base. But we will still add the following features that not existing in the tutorial so far:
- A new data structure called `idea` , 
- A SQL data structure to store idea content and voter address and deposit amount
- Modify the UI from task creater to idea creater. Make the content larger, since the idea may need more content than the original task content
- Modify the `worker` UI logic to `voter` UI logic. The "take" button changes to "vote", and allow to add amount
- Add backend logic to handle voter voting event. Adding deposit 
- Add backend logic to calculate the total voter and creater's deposit. Use this total to sort in SQL
- Remove the `worker` take and confirm logic. We do not have such logic for now.

Once we have the above changes, the original tutorial will turn into the new TApp Idea Voting app.

Next, let's start system design!
