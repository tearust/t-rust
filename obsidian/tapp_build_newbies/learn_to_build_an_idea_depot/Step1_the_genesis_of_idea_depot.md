# Step 1: The Genesis of Idea Depot
## The Genesis of the Idea Depot App
We at the TEA Project are often asked what kind of apps they should build using the forthcoming TApp SDK and dev tutorials. Developers are curious about what apps best utilize TEA's decentralization, secure enclaves, and unstoppable compute layer. There has been a lot of discussion in our community with many of our community members contributing their own creative ideas. These include:

- Social media apps.
- Wager-based apps.
- Bridging web2 apis to unlock web3 functionality.

We have a way to capture such ideas from our community through Github discussions. For example, we've already made a voting page specifically about expanding functionality for our Task TApp which you can find [here](https://github.com/tearust/teaproject/discussions/132).

Our main **teaproject** Github repo is open and anyone can create a discussion about ideas they have. But that got us thinking: why not use the TEA Project platform itself to mimic this functionality? Since we're a software platform, why don't we make this idea (of gathering and voting for ideas) into a TApp? So this is what prompted us to write this build series of posts that show developers can create their own voting application.

## What Will the Idea Depot App Do?
Many content management systems in the web2 world have some functionality for voting on different topics to gauge what's most popular with the community. For example, this is what it looks like in the Zendesk CRM:

![olcYhlMDSPajiBKcSBWh5Q](https://github.com/tearust/teaproject/assets/86096370/96c4dc4d-2a75-48f8-8411-5b6ee125ae37)

We want to unlock the same functionality on web3 specifically as a TApp on the TEA Project platform. We're going to be building a TApp that collects ideas for apps and allows voting on them. For our app idea we're going to implement the following features:

- Everyone can post ideas about what TApps they want to build. For every TApp idea they post they'll need to put up at least 1 T as an initial deposit for each idea.
- Everyone in the community can vote to support an idea by locking as collateral for their vote of at least 1T. All T collateral that's put up for votes will be added to the initial deposit. 
- Once a user has voted, they cannot withdraw their T vote collateral. But they can increase their collateral deposit by voting again and adding at least 1T to their existing pledge.
- The ideas are ordered by the total deposit count. The higher the vote total, as measured by the total vote collateral (including initial deposit), the higher its position will be in the app.
- We currently don't have a worker feature for users to complete the voted on idea as a task. So although an app idea can only remain in this TApp indefinitely, in the future this feature could be added.

One of the last missing pieces for this app is what to do at "task completion". We can imagine these ideas for apps as being tasks waiting for completion, where "task completion" means completing the app idea and making it into a functional TApp. Perhaps the app will have another segment where the collected deposit for each idea will be released from escrow to a developer when certain conditions are met. This is beyond the scope of this writeup but we invite developers to think up creative ways to reward app developers for taking on the ideas as tasks.

## Moving from Web2 to Web3
As mentioned, this kind of idea voting is a typical and easy to implement as a web2 app. We have to move beyond the traditional web framework when designing such an app for web3. In the next section, we'll look at how to design such a voting app from the ground up as a web3 app using the TEA Project framework.



