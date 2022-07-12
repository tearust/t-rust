# Gist

This article explains the workflow from the very beginning as any TApplication is loaded into the user's browser.

These steps include:

* How the end user loads the bootloader js code.
* How the bootloader finds the TEA Project layer1 smart contract on Ethereum.
* How the bootloader js finds the hosting nodes for the TAppStore.
* How the hosting nodes find  [obsidian/harberger_tax/Global_state_maintainer_nodes](../harberger_tax/Global_state_maintainer_nodes.md).
* How the first txn goes through (eg. Login).

The design of the bootloader has the following assumptions:

* The end user has a modern browser and can connect to the internet.
* The end user has Metamask extension installed in the browser.
* The end user can somehow download a bootloader js file (a static plain text file).
* The Ethereum network is live and at least some of the nodes are reachable.
* The TEA netowrk is live and at least some of the nodes are reachable.

The design of bootloader does **not** require the following assumptions:

* DNS is working. eg. something.teaproject.org is working.
* All TEA nodes are reachable.
* HTTPS SSL certification.

The goals of the design are:

* Nonstoppable TApplications. This means no one (ISP, cloud provider) can stop the TApps.
* No single point of failure. This means as long as a minimal number of nodes are running, the system works as usual.
* Domain name is not required.  This means the app builder or miners does **not** need to apply for an SSL domain certification.

# Basic workflow

## End users receive a bootloader js code

There are endless ways to get such a plain text js code. If the DNS is working, the user can just go to wallet.teaproject.org or any other supported website to start. The website will auto download the bootloader js to the user's browser. The user can download to their local file system for future use (in case the domain fails), as well as send this file to any friend using any communication protocol (IPFS, IM, email, FTP, USB drive etc). Note, DNS and wallet.teaproject.org is optional.

This file is a static file. It doesn't need changing as long as the Ethereum network doesn't change.

This file is an entry point to the whole TEA Project world.

## Bootloader js connects the Ethereum network

When the bootloader is loaded into the browser (either load local static file, or from wallet.teaproject.org), the js code will try to connect to the Metamask in the browser. Using Metamask (web3.js) the bootloader js can connect to Ethereum network. Once connected, it will make a remote procedure call (RPC) to any Ethereum nodes to query the **list of currently active hosting nodes** for the TAppStore application. 

The list is dynamically updated in the TEA Project's layer 1 smart contract. That means every time any one queries (RPC) the TEA Project smart contract, a current active list of IP address will be returned to the caller. 

The end user can either mannually or automatically select a nearby (nearby means faster ping) any available hosting TEA node as the **host**. They will then go to that IP address to continue using the TAppStore app.

## TAppStore app loads into the user browser

By clicking the selected hosting node address, the end user's browser goes to the TAppStore app. Because all the hosting nodes are the same, no matter which IP address the user selects, the same TAppStore app will be loaded into the end user's browser.

From now on, the bootloader has accomplished its mission. The following steps only relate to the TAppStore application.

## TAppStore uses TEA network (a P2P network) to find the layer2 state machine.

This happens between hosting nodes and [obsidian/harberger_tax/Global_state_maintainer_nodes](../harberger_tax/Global_state_maintainer_nodes.md). All of them are connected together using the TEA Network, a peer to peer network. The end user doesn't need to know anything about this. The end user only connects to one of the hosting nodes, and the rest would be handled by the hosting nodes.

The TEA network is a big topic that we'll have to skip in this article. You can just simply assume the hosting node has already connected to the [obsidian/harberger_tax/Global_state_maintainer_nodes](../harberger_tax/Global_state_maintainer_nodes.md). It knows how to get the latest state update, and how to send the  query and command to the state machine. 

The hosting nodes don't need to connect to the Ethereum network directly. They only need to connect to the state machine, anything else would be handled by the state machine.

## End user launches any TApp

Right now, the end user has loaded the TAppStore. Inside TAppStore UI, a list of TApps can be selected by the end user. If the end user loggedin, he can also find his own favorite apps in list. He can click any of them to launch the TApp in his browser.

Because the end user's browser has connected to the TEA project (at least the TAppStore as the entry point), they can connect to any hosting nodes and launch any TApps without relying on centrlaized services (DNS, HTTPS). 

# Design considerations

We minimized the requirement to a static plain text bootloader js file. This can reduce the requirement for developers to register a domain name, an SSL https certificate, a cloud provider IP address. All these requirements can cause centralized control. Since we're making a nonstoppable app, all these requirements need to be removed.

The looking up steps can be simplifed as: Static bootloader js file -> hardcoded Ethereum network and TEA Project smart contract address -> Query active hosting nodes address for TAppStore -> Launch TAppStore from any of these hosting nodes. -> TAppStore connect to state machine and list all avilable TApps -> Select any TApp from TAppStore -> Run this TApp.

# Future TEA Box improvements

The above workflows are just for the end user browser use case. If the end user has their own TEA box at home as the Web3 gateway, things would be much easier.

They would then only need to do is go to the IP address of their own TEA box in their local LAN (for example, 192.168.1.100). This will load the TAppStore locally. Because the TEA Box is a **private** hosting node for this specific end user, there's no need to look up in Ethereum and there's no need to get services from other miners' hosting nodes. It's actually a cost-free entry point to the TEA Project. That's why we call the TEA Box the **Personal Gateway of Web3**.
