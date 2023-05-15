# Step by Step Tutorial

The tutorial section is designed to walk you through creating a TEA Project TApp that run as decentralized full compute-capable dApps. Developing a TApp is very similar to developing a typical web2 application. Even though TApps are "web3, you don't need to learn smart contracts or understand distributed systems. But there are still many differences with web2 apps and this tutorial is here to help you overcome these challenges. For more information on what skills are required to complete this tutorial as well as the proficiency required to be a TApp developer, read more in [dev prerequisites](../010\_core\_docs/010\_Developer\_requirements.md).

During this tutorial, we'll build a simple TApplication step by step by cloning from a boilerplate repo called **tutorial-v1** and testing locally using the **dev-runner** repo. The app you'll build looks just like a typical "todo list" example you might've seen elsewhere in various web framework tutorials but we have a bit of an improvement. The worker who completes a task will get paid by the owner who created the task.

![Pasted image 20230319210108.png](<../../../obsidian/Pasted image 20230319210108.png>)

The tutorial is sectioned into multiple parts that build upon each other.

* The [setup section](010\_install-dev-env/) goes over the software that needs to be installed in your development environment.
* From there you can get started on a simple ["hello world" app](020\_hello\_world/021\_local\_build\_and\_unit\_test/) and get the UI to [display hello world](020\_hello\_world/022\_dev-runner\_the\_local\_development-environment/).
* Next up is the [deploy section](030\_deploy\_helloworld\_testnet/) that shows you how to register your TApp as a real app running on the TEA network and uploading it to IPFS.

In the next section we actually start working on the actual todo list TApp.

* First up we learn how to implement [login with Metamask](040\_add\_login\_feature/) feature for our TApp where the user identity is tied to an Ethereum address. This section also introduces the faucet which endusers can use to get initial funds.
* Our TApp will need to store task data so we'll need to [initialize an SQL database](050\_sql\_crdt/) in this step of the tutorial. The TEA Project offers an SQL database that's available to all TApps in the ecosystem.
* Next up is implementing [fund transfer](060\_reward\_fund\_transfer/) in our app so task workers can get paid for their work.
* And finally we [add gas payments](070\_gas\_fee\_payment/) into our simulated platform to make it as close as possible to the TEA Project mainnet. In web3, those that provide the compute infrastructure are compensated with gas payments. This step creates new log page which includes the gas payment log.

After following the full tutorial, you can read a [summary of what we've accomplished](080\_summary/). You shoulnd now be able to create your own TApps by adding your own business logic to the boilerplate.
