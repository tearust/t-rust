# Step 4: Run Tutorial Locally
Before getting started building my first TApp, it makes sense to go through the tutorials already listed in the [TEA Project's dev docs](https://dev.teaproject.org/020_tutorial). It looks like they mostly use the same repo called **tutorial-v1** and that you checkout a branch based on what features you want to try out. Just reading them over this is what I can gather:

- The first actual tutorial on building an actual TApp where someone can login with Metamask starts with the aptly named [login branch of the tutorial](https://dev.teaproject.org/020_tutorial/040_add_login_feature).
- It looks like the tutorial goes over how to build a decentralized task app that includes confirmation that task is done as well as funds transfer. This is really cool and could be the basis of a decentralized Fiverr type of dApp. Really impressed that they released a fully-featured tutorial app like that.
- The next step involves the `SQL` branch so named for the database because that step of the tutorial focuses on implementing the database structure.
- Then there's the `reward` branch which goes over fund transfer.

Since every branch builds on the last, I thought it was a bit tedious to checkout each branch, do the actual tutorial, then tear it down and checkout the next branch. So I decided to read over the `login` and `sql` branch sections and decided to dive in at the `reward` branch.

## Follow the bouncing ball between the two repos

To get started with the `reward` branch I download the **tutorial-v1** repo and switch branches:

```
git clone https://github.com/tearust/tutorial-v1.git
git checkout reward
```

I'll be following the [reward branch tutorial](https://dev.teaproject.org/020_tutorial/060_reward_fund_transfer) and taking notes here. The actual steps can be found in that link so I won't bother repeating verbatim what you can already read there, I'll just be providing my notes as I go through the process.

One thing to keep in mind is that you have to have the `dev-runner` environment up to run your tutorial code on top of. So this was the first part that was a little confusing and I'll paraphrase here as best I can.

- Now that I've installed the `tutorial-v1` and switched to the `reward` branch, it's time to install the dev environment.
- I followed the [install instructions for dev-runner](https://dev.teaproject.org/020_tutorial/010_install-dev-env): `git clone https://github.com/tearust/dev-runner.git`. There's plenty of other things mentioned on that page that you should install. One of the biggest takeaways that tripped me up when trying to run `npm start` later in the tutorial was making sure I was running node version 14.14.0.
- Now when you run the `build.sh` scripts in the `tutorial-v1` repo, these will output wasm files into the `dev-runner` local folder. These are the actor files.

That's what I mean by "follow the bouncing balls" as the two repos are interlinked. I first had to run the build.sh scripts in `tutorial-v1` and only after those actor files were outputed could I then jump back into the `dev-runner` repo and start up the local dev environment with `docker-compose up`.

## Launching the actual app
With the preliminaries out of the way, I jumped back into `tutorial-v1`, specifically the `sample-front-end` directory and ran the following commands:
```
npm install
npm start
```
I was really excited to access the TApp in the browser by launching [http://localhost:3200](http://localhost:3200/).

I tried to login and was met with this:

![2023-05-12 11 12 43](https://github.com/tearust/teaproject/assets/86096370/62b72f26-4b97-4cef-8beb-e30461df615b)

I got a little ahead of myself as I forgot to go to the admin tab and press the two initialization buttons first:

![](https://user-images.githubusercontent.com/86096370/227608431-89da24e9-03d6-4e91-a28e-e14f63d02952.png)

Launching the TApp the first thing you probably want to do is login so don't forget that important initialization step.

Another issue I found is that when I returned to do this tutorial again, I got an error message when I tried to initialize the app:

![2023-05-12 11 11 06](https://github.com/tearust/teaproject/assets/86096370/287edf10-9521-46ed-9fe6-ac82b9b0e4da)

I found out that this involves the dev-runner docker images being out of date. To fix, you'll need to run `docker-compose down` to clear the container and then check for updates to the docker images. There are two images which you can update using `docker pull` to get the latest docker images (the actual commands are available in [the dev-runner faq](https://dev.teaproject.org/020_tutorial/010_install-dev-env#troubleshooting-the-dev-environment)). Then you'll run `docker-compose up` to reload the `dev-runner` environment.


