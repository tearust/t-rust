# Step 4: Run Tutorial Locally

Before getting started building my first TApp, it makes sense to go through the tutorials already listed in the [TEA Project's dev docs](https://dev.teaproject.org/020_tutorial). It looks like they mostly use the same repo called **tutorial-v1** and that you checkout a branch based on what features you want to try out. Just reading them over this is what I can gather:

* The first actual tutorial on building an actual TApp where someone can login with Metamask starts with the aptly named [login branch of the tutorial](https://dev.teaproject.org/020_tutorial/040_add_login_feature).
* It looks like the tutorial goes over how to build a decentralized task app that includes confirmation that task is done as well as funds transfer. This is really cool and could be the basis of a decentralized Fiverr type of dApp. Really impressed that they released a fully-featured tutorial app like this.
* The next step involves the `SQL` branch so named for the database because that step of the tutorial focuses on implementing the database structure.
* Then there's the `reward` branch which goes over fund transfer.

Since every branch builds on the last, I thought it was a bit tedious to checkout each branch, do the actual tutorial, then tear it down and checkout the next branch. So I decided to read over the `login` and `sql` branch sections and decided to dive in at the `reward` branch.

## Follow the bouncing ball between the two repos

To get started with the `reward` branch I download the **tutorial-v1** repo and switch branches:

````
git clone https://github.com/tearust/tutorial-v1.git
git checkout reward
````

I'll be following the [reward branch tutorial](https://dev.teaproject.org/020_tutorial/060_reward_fund_transfer) and taking notes here. The actual steps can be found in that link so I won't bother repeating verbatim what you can already read there, I'll just be providing my notes as I go through the process.

One thing to keep in mind is that you have to have the `dev-runner` environment up to run your tutorial code on top of. So this was the first part that was a little confusing and I'll paraphrase here as best I can.

* Now that I've installed the `tutorial-v1` and switched to the `reward` branch, it's time to install the dev environment.
* I followed the [install instructions for dev-runner](https://dev.teaproject.org/020_tutorial/010_install-dev-env): `git clone https://github.com/tearust/dev-runner.git`. There's plenty of other things mentioned on that page that you should install. One of the biggest takeaways that tripped me up when trying to run `npm start` later in the tutorial was making sure I was running node version 14.14.0.
* Now when you run the `build.sh` scripts in the `tutorial-v1` repo, these will output wasm files into the `dev-runner` local folder. These are the actor files.

That's what I mean by "follow the bouncing ball" as the two repos are interlinked. I first had to run the build.sh scripts in `tutorial-v1` and only after those actor files were output could I then jump back into the `dev-runner` repo and start up the local dev environment with `docker-compose up`.

## Launching the actual app

With the preliminaries out of the way, I jumped back into `tutorial-v1`, specifically the `sample-front-end` directory and ran the following commands:

````
npm install
npm start
````

I was really excited to access the TApp in the browser by launching [http://localhost:3200](http://localhost:3200/).

I tried to login and was met with this:

![2023-05-12 11 12 43](https://github.com/tearust/teaproject/assets/86096370/62b72f26-4b97-4cef-8beb-e30461df615b)

I got a little ahead of myself as I forgot to go to the admin tab and press the two initialization buttons first:

![](https://user-images.githubusercontent.com/86096370/227608431-89da24e9-03d6-4e91-a28e-e14f63d02952.png)

Launching the TApp the first thing you probably want to do is login so don't forget that important initialization step.

Another issue I found is that when I returned to do this tutorial again, I got an error message when I tried to initialize the app:

![2023-05-12 11 11 06](https://github.com/tearust/teaproject/assets/86096370/287edf10-9521-46ed-9fe6-ac82b9b0e4da)

I found out that this involves the dev-runner docker images being out of date. To fix, you'll need to run `docker-compose down` to clear the container and then check for updates to the docker images. There are two images which you can update using `docker pull` to get the latest docker images (the actual commands are available in [the dev-runner faq](https://dev.teaproject.org/020_tutorial/010_install-dev-env#troubleshooting-the-dev-environment)). Then you'll run `docker-compose up` to reload the `dev-runner` environment.

Next I was able to login with Metamask and activate the faucet as well as set the spending limit so I could use the TApp.

![](https://user-images.githubusercontent.com/86096370/227608440-e8c2de65-149a-4de1-8051-f19028f7551b.png)

## Using the actual app

I actually needed two Metamask accounts to test the logic of the app (one for the task creator and the other for the task taker). Once the task maker has created the task, the task taker will claim it and begin working on it.

![take2F](https://github.com/tearust/teaproject/assets/86096370/5ee24f29-d3b9-42b7-a47f-21e2ad3b886b)

One thing that through me for a loop was that some funds were removed from the task taker's account when they took the task. Evidently this is done as a way to ensure that task takers don't indiscriminately take tasks, otherwise they could hoard all the tasks for themselves. There is some opportunity cost related to taking a task which is fair from the perspective of the system. The issue I see is that the task creator can decline the submission for whatever reason and the task taker doesn't get their funds back. This is an issue that a creative dev can solve with some forethought balancing the needs of fairness on both sides.

As mentioned, the task creator alone can decide whether the task has been done up to standards or not.

![confirmRrzDz](https://github.com/tearust/teaproject/assets/86096370/e9f5fc3d-80e0-43b3-b0f0-041760fd76c1)

This I see as a potential for abuse as anyone who wants work done can create a task, reject it even when a good job is submitted, then claim the task with another account and sign off on it to claim the bounty. This problem is remedied by using a secure oracle to decide if the task is done properly or not without human intervention, something the TEA Project shows in their [retweet task example](https://dev.teaproject.org/020_tutorial/065_retweet_task).
