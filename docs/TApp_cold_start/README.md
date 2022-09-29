# Gist

This article explains the workflow from the very beginning as any TApplication is loaded into the user's browser.

These steps include:

* How the end user get the TApp CID.

* How the end user loads the bootloader js code.
* How the bootloader finds the TEA Project layer1 smart contract on Ethereum.
* How the bootloajjder js finds the hosting nodes for the TAppStore.
* How the hosting nodes find  [Global_state_maintainer_nodes](../harberger_tax/Global_state_maintainer_nodes.md).
* How the first txn goes through (eg. Login).

The design of the bootloader has the following assumptions:

* The end user has a modern browser and can connect to the internet.
  . The end user has access to any IPFS gateway, or has IPFS extension installed.
* The end user has Metamask extension installed in the browser.
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

## End user find a CID of desired TApp

CID(Content ID) is an IPFS term. As long as you have the CID, you can download the coresponding data from any IPFS node.

Our TApp's front end code (html, js, css in a package) is stored in IPFS. As long as the end user know the latest CID of front end code, he can go to any IPFS gateway or local IPFS extension to load the front end code into browser.

End users can get CID of any TApp by any of the following methods:

* He can access to wallet.teaproject.org
* He can access to a local run TAppStore tapp. (this is the same as wallet.teaproject.org)
* He can use tools like Etherscan to lookup CID from our smart contract "tapps" storage. This require higher tech skill, but someone may build a tool to do that.
* He receive the CID from any social media  (He will need to verify its source manually)
* He knew and saved the CID locally

## End users load front end code from IPFS

Either go to IPFS gateway, or use local IPFS extension, user can easily download the execute the front end code as long as he knows the CID from the previous step.

Once the code loaded into the browser, a bootload js module is loaded and executed on load. It will look up Ethereum to find the IP URL of hosting nodes that currently hosts this Tapp.

## Bootloader js connects the Ethereum network

When the bootloader is loaded into the browser, the js code will try to connect to the Metamask in the browser. Using Metamask (web3.js) the bootloader js can connect to Ethereum network. Once connected, it will make a remote procedure call (RPC) to any Ethereum nodes to query the **list of active bootstrap hosting nodes** for the TAppStore application. 

The list is dynamically updated in the TEA Project's layer 1 smart contract. That means every time any one queries (RPC) the TEA Project smart contract, a current active list of IP address will be returned to the caller. 

The bootstrap js continue to query any of these bootstrap nodes for **a list of active hosting nodes for this TApp**. This time, the requested node will query the layer2 state and response the list of current active hosting nodes IPs of this specific TApp.

The end user can either mannually or automatically select a nearby (nearby means faster ping)  IP from the responding hosting nodes as the **host or "server"**. All the future API call will be sent to this IP address.

## TAppStore uses TEA network (a P2P network) to find the layer2 state machine.

This happens between hosting nodes and [Global_state_maintainer_nodes](../harberger_tax/Global_state_maintainer_nodes.md). All of them are connected together using the TEA Network, a peer to peer network. The end user doesn't need to know anything about this. The end user only connects to one of the hosting nodes, and the rest would be handled by the hosting nodes.

The TEA network is a big topic that we'll have to skip in this article. You can just simply assume the hosting node has already connected to the [Global_state_maintainer_nodes](../harberger_tax/Global_state_maintainer_nodes.md). It knows how to get the latest state update, and how to send the  query and command to the state machine. 

The hosting nodes don't need to connect to the Ethereum network directly. They only need to connect to the state machine, anything else would be handled by the state machine.

## End user launches any TApp

Any TApp will follow the same rule explained above. TAppStore is also a TApp, but it is a portal of all other TApps. It is most likely the first TApp an end user start to use.

Once the end user loaded the TAppStore(either from CID, or wallet.teaproject.org). Inside TAppStore UI, a list of TApps can be selected by the end user. If the end user loggedin, he can also find his own favorite apps in list. He can click any of them to get the CID. Therefore launch the TApp in his browser.

# TApp front end initialize and upgrade

There are tables in both layer2 and layer1

|tapp_id|name_version|cid|
|-------|------------|---|
|0|TAppStore_1.0.2|Qm12345...|
|1|TeaParty_0.2.1|QmABCDE...|

In layer2, when this app developer first init or update newer version of front end code, they will need to upload to IPFS. Get the CID, then sign a txn to layer2 to "upsert" this item in the table.

Our layer2 state maintainer will validate the txn, then update the table in layer2. After that, they will co-sign a layer1 txn to layer1 to update the smart contract table.

The CID in this table is always the latest.

## What if end user start an older version of CID?

Since the TApp continue update. It is very common that end user launch an older version (older CID).

All the bootload js code has a "find latest CID" logic. Once it launch, it will look up layer1 smart contract for latest CID. If found, it will prompt end user to re-launch the latest one. 

Fail or refused to do so may cause future API call to hosting node get rejected.

# Design considerations

We minimized the requirement to a IPFS loader and static bootloader js file. This can reduce the requirement for developers to register a domain name, an SSL https certificate, a cloud provider IP address. All these requirements can cause centralized control. Since we're making a nonstoppable app, all these requirements need to be removed.

The looking up steps can be simplifed as: Get the CID -> Load front end code from IPFS -> Execute static bootloader js file -> hardcoded Ethereum network and TEA Project smart contract address -> Query latest CID(if need upgrade) and active hosting nodes address for TApp-> Send login request to one of the hosting node....

As long as the Ethereum is running, IPFS is running, TEA Project is running, the whole workflow has no dependancy of DNS, SSL. No single point of failure.

# Future TEA Box improvements

The above workflows are just for the end user browser use case. If the end user has their own TEA box at home as the Web3 gateway, things would be much easier.

They would then only need to do is go to the IP address of their own TEA box in their local LAN (for example, 192.168.1.100). This will load the TAppStore locally. Because the TEA Box is a **private** hosting node for this specific end user, there's no need to look up in Ethereum and there's no need to get services from other miners' hosting nodes. It's actually a cost-free entry point to the TEA Project. That's why we call the TEA Box the **Personal Gateway of Web3**.
