# Deploy Hello World on Testnet

**NOTE: this section is currently under development. There's currently no DevPortal URL. When the link becomes available, we'll remove this notice when it's ready to be deployed**

In this section of the tutorial, we'll use the dev portal to deploy the **Hello World** boilerplate to the TEA testnet. The goal is to see the `Hello [developer's name]` TApp in the browser after launching such a TApp from the TAppStore.

## Prerequisites

Besides the prereqs stated earlier, this tutorial involves uploading to IPFS and will require [installing the IPFS command line tool](https://docs.ipfs.tech/install/command-line/#system-requirements). For those running MacOS it's also available through Homebrew:

`brew install ipfs`

You'll also need some TEA funds in your wallet to use the Developer Portal. You can request funds by sending us a message in our [Telegram group](https://t.me/teaprojectorg).

## Clone the tutorial master branch

Run `git clone https://github.com/tearust/tutorial-v1.git` to clone code to local. Recall that there are two folders in the root of the **tutorial-v1** code repo:

* sample-actor (the back end lambda function).
* sample-front-end (the front end code).

If you'd like to check in any of your modified code to your own git repo, please rename the project to avoid any conflicts.

## Setup TApp

* Login to the Devportal with Metamask: (link will be updated here when ready)
* You can see your account balance after login. 
* Go to the TApps page in the Devportal. Here you'll create a new TApp and save the token_id:

<img width="1198" alt="Screenshot 2023-04-14 at 4 52 21 PM" src="https://user-images.githubusercontent.com/86096370/232627849-ca3a14cb-d3b5-4358-b022-ee86652d7187.png">

We will add this token_id minus the leading `0x` to `~/sample-actor/impl/manifest.yaml` at line 3. You should also change the actor name from `sample-actor` to a unique name.

![image](https://user-images.githubusercontent.com/3214173/231840591-775730aa-1900-4c76-adb6-791f9dd2f467.png)

## Build and upload actor to IPFS

You can build your actor using the same commands we used in the previous step:
`cd sample-actor` and run `./build.sh` to build the actor. 

To upload the actor to IPFS, run `./ipfs.sh` from the same directory which executes the following script:

![image](https://user-images.githubusercontent.com/3214173/231841142-35201bb1-a818-4dc0-b754-d9fca8e04b51.png)

Save the CID returned from the script to use in the next step.

![image](https://user-images.githubusercontent.com/3214173/231841451-4587904d-7e11-4689-b1ae-f308dd6bacb6.png)

## Update TApp metadata in the Devportal

Go back to the Devportal and click the cog icon next to your TApp listing in the **Developer** tab. 

![image](https://user-images.githubusercontent.com/3214173/231842662-30bf5a95-a2ec-47d0-93d1-b1b861bdb463.png)

Enter in the information as instructed in the following image to update your TApp's metadata:

![image](https://user-images.githubusercontent.com/3214173/231844198-11dceef7-c2d8-45a4-b636-9cc16b52d5c4.png)

If you get a **spend_over_allowance** error:

![Screenshot 2023-04-18 at 12 18 17 PM](https://user-images.githubusercontent.com/86096370/232887275-9d7cfb59-dd72-4537-a247-f0225c787b9b.png)

Then you'll need to increase the spending limit for the **Developer Portal** in the [Nitro TAppStore](http://54.180.82.194:8080/ipfs/QmS5K9u8rfWpAxgonJeB4pX1qMyBqpz9A8Etb2GuTFFhts/):

![Screenshot 2023-04-18 at 12 20 53 PM](https://user-images.githubusercontent.com/86096370/232887289-2a055984-dd9a-4ef5-a763-92d6d2966940.png)

Here you can set the spending limit up to the amount you have in your TEA wallet:

![Screenshot 2023-04-18 at 12 29 24 PM (2)](https://user-images.githubusercontent.com/86096370/232887293-16ba1b14-10b3-473c-b38c-f8170ee02630.png)

## Build and upload front-end

Build the front-end code (jacky_test/sample-front-end/) and upload to IPFS using  `~/sample-front-end/ipfs.sh`.

Upon succesful completion you'll save the CID of the front-end to use in the next section (the CID is on the last line ending with `dist`).

![image](https://user-images.githubusercontent.com/3214173/231847211-848c89f0-f0ec-4d2a-ae8e-0b908b8e34c3.png)

## Update the front-end CID in Devportal

Edit your TApp and enter in the CID for the front-end: 

![image](https://user-images.githubusercontent.com/3214173/231859470-e13f5b74-a345-46d4-8172-529ac0e203d0.png)

* Visit front-end code in ipfs using the URL format `http://54.180.82.194:8080/ipfs/[CID]/#/welcome`

In our current example it would be `http://54.180.82.194:8080/ipfs/QmdGoyx5JLinEPM8ZiMNaXkVjbqmg2rWygvxPBWPNKou4A/#/welcome`

Note that the actor will be loaded after the first request. If you see an error "you need to try later", that means the node is loading the actor and will be available when you try later after it's been loaded.

## Activate the TApp in the Devportal

Activating the TApp will make it available on the TEA network. To activate your TApp, press the start button next to it in the Devportal:

![image](https://user-images.githubusercontent.com/3214173/231850827-97495908-f12d-44fe-b1d3-59078ec4778a.png)

Once it's active, other users can launch the TApp through [the TAppstore](http://54.180.82.194:8080/ipfs/QmS5K9u8rfWpAxgonJeB4pX1qMyBqpz9A8Etb2GuTFFhts/).

![image](https://user-images.githubusercontent.com/3214173/231851272-a56a99b0-a7ff-404d-be1a-1bf5c2d16bd8.png)
