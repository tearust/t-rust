This article explains the workflow from the very beginning to loaded any TApplication to his browser.

The steps contains
- How the end user load the bootloader js code
- How the bootloader find TEA Project layer1 - the TEA Project smart contract on Ethereum
- How the bootloader js find the hosting nodes for TAppStore
- How the hosting nodes find  [[obsidian/harberger_tax/Global_state_maintainer_nodes]]
- How the first txn go through (eg. Login)

The design of bootloader has the following assumption:
- The end user has a modern browser and connect to internet
- The end user can somehow download a bootloader js file (a static plain text file)
- The Ethereum network is live. Some of the nodes are reachable.
- The TEA netowrk is live. Some of the nodes are reachable.

The design of bootloader does **not** require the following assumption:
- DNS is working. eg. something.teaproject.org is working
-  All TEA nodes are reachable.
- HTTPS SSL certification.

The goals of the design are
- Nonstoppable TApplications. This means no one (ISP, cloud provider) can stop the TApps
- No singe point of failure. This means as long as a minimal nodes are running, the system works as usual.
- Domain name is not required.  This manes the app builder or miners does **not** need to apply for an SSL domain certification.

# Basic workflow
## End users receive a bootloader js code
There are endless ways to get such a plain text js code. If the DNS is working, user can just go to wallet.teaproject.org or any other supported websites to start. The website will auto download the bootloader js to the user browser. The user can download to local file system for future use (in case of the domain fail), or furthermore send this file to any friend using any communication (IM, email, FTP, USB drive etc).

This file is a static file, it doesn't need change as long as the Ethereum network do not change.

This file is the entry point of the whole TEA Project world

## Bootloader js connects the Ethereum network 
The Ethereum network bootstrap nodes and TEA Proejct layer1 smart contract address are hardcoded into the bootloader js file. Then it is laoded into the browser, the js code will try to connect to the Ethereum network. Once connected, it will RPC any Ethereum nodes to query the **list of currently active hosting nodes** of TAppStore application. 

The list is dynamically updated in the TEA Project layer 1 smart contract. That means every time any one query (RPC) the TEA Project smart contract, a current active list of IP address will be returned to the caller. 

The end user can either mannually or automatically select a nearby (nearby means faster ping) hosting TEA node as the **host**, go to that IP address to continue

## TAppStore app loads into the user browser
By clicking the selected hosting node address, the end user browser goes to the TAppStore app. Because all the hosting nodes are the same, no matter which IP address the user select, the same TAppStore app will be loaded into the end user browser.

From now on, the bootloader has mission complished, the following steps only related to TAppStore application.

## TAppStore uses TEA network (a P2P network) to find layer2 state machine.
This happens between hosting nodes and [[obsidian/harberger_tax/Global_state_maintainer_nodes]]. All of them are connected together using TEA Network, a peer to peer network. The end user doesn't need to know anything about this. The end user only connect to one of the hosting nodes, the rest would be handled by the hosting nodes.

The TEA network is a big topic that we have to skip in this article. You can just simply assume the hosting node has already connect to the [[obsidian/harberger_tax/Global_state_maintainer_nodes]]. It knows how to get state update, how to send the  query and command to the state machine. 

The hosting nodes do not need to connect to the Ethereum network directly. They only need to connect to the state machine, anything else would be handled by the state machine.

## End user launch any TApp
Right now, the end user has loaded TAppStore. Inside TAppStore UI, a list of TApps can be selected by the end user. If the end user loggedin, he can also find his own favorite apps in list. He can click any of them to launch the TApp in his browser.

Because the end user's browser has connect to the TEA proejct (at least TAppStore as the entry point), he an connect to any hosting nodes and launch any TApps without rely on DNS, HTTPS centrlaized services. 

# Design considerations
We minimized the requirement to a static plain text bootloader js file. This can reduce the requirement for developers to register a domain name, a SSL https certificate, a cloud provider IP address. All these requirements can cause centralized control. Since we are making a nonstoppable app, all these requirements need to be removed.

The looking up stpes can be simplifed as: Static bootloader js file -> hardcoded Ethereum network and TEA Project smart contract address -> Query active hosting nodes address for TAppStore -> Launch TAppStore from any of these hosting nodes. -> TAppStore connect to state machine and list all avilable TApps -> Select any TApp from TAppStore -> Run this TApp.

# Future TEA Box improvements
The above workflows are just for the end user browser use case. If the end user has his own TEA box at home as the web3 gateway, things would be much easier.

He only need to do is go to the IP address of his own TEA box in his local LAN. For example, 192.168.1.100. This will load TAppStore locally. Because the TEA Box is a **private** hosting nodes for this specific end user. There is no need to look up in Ethereum, there is no need to get service from other miners' hosting nodes. It is actually cost-free entry point to TEA Project. That is why we call the TEA Box the **Personal Gateway of Web3**.

