# Introduction
Developing a TApp is very similar to developing a typical web2 application. You don't need to learn smart contracts, or understand the concept of distributed systems. But there are still many differences. This tutorial is here to help you overcome these challenges. 

During this tutorial, we'll build a simple TApplication step by step. It's called **Tutor-v1"**. It looks like a typical "todo list" example we have seen so many times in various web framework tutorials but we have a bit of an improvement. The worker who completes a task will get paid by the owner who created the task. 

![[Pasted image 20230319210108.png]]

During our tutorial, we will:

- Setup the development environment. 
- Clone from the boilerplate. 
- Test locally using `dev-runner`.
- Deploy to the testnet.
- Gradually add features to the app.
- Summarize what we've accomplished.

After following the full tutorial, you should be able to create your own TApps by adding your own business logic to the boilerplate. But you may need a more detailed explanation, in which case you should read our development documentation.

## Try before build

This tutorial will culminate in building a decentralized gig task app similar to Fiverr.com. Before actually taking all the steps to build the app, users can run the app on our AWS Nitro deployment at http://43.201.23.47:8080/ipfs/QmberYsF6kSVbrxzp1DZuMtcZTfvCWG13ZePLi4PVcHyY8/#/discover

Just login with your Ethereum account using Metamask and click on **tutorial-v1** to start the app.

Instructions to use this app are explained [elsewhere in the tutorial](020_tutorial/060_reward_fund_transfer/README.md).
