# Gist

Similar to Zapier, IFT3 is a business automation tool. Business people can build IFTTT logic by connecting executable modules(we name them Actors) with business condition, so that the business workflow can run automatically, and more important **decentralized**. 

# Web3 app should belong to owner not platform

In Web2 world, the relationship between platform and users are unbalanced. Although users gereate content, but the platform (or the owner of the platform) has super power to control over users. If platform stopped working, the content or the assets that built by user on the platform would be in danger.

TEA as a web3 app runtime, we decide to build a different platform and user relationship in the following ways:

* User build apps that belongs to the user.
* App's data is ownered by the app.
* TEA Runtime only charge usage fee based on how much resources the app uses

These relations are made possible by the factor that TEA nodes load user's app into a hardware enclave. All the secrets are decrypted in the enclave only, so that node owner or TEA state maintainers have no mean to peek. There is no central control in TEA ecosystem. That means as long as a single TEA node is alive, end user can launch a TApp on that node. 

# Business TApp should be a logic tree composed by a group of actors

Actors are the Webassembly binary module that can be loaded into TEA runtime. They are pure functions that runs to handle input (request) and returns output(response). The end users can link those input and output with conditional routes to a logic tree. The logic tree runs automatically to implement a business logic.

An existing real world example is Zapier. User doesn't need to have programming skill. They just simplify a complex business logic into multiple steps. Each step has **trigger** and **action**. Trigger means **if something happen**, the **action** means **then do this**.  The beauty of Zapier is that the user can build their own business logic automation and runs in the Zapier platform **with zero coding**.

TApps can be such business automation too. Similar to Zapier, the hardcore developers can build basic actors as Webassembly binary modules. Busienss users just connect these modules with conditional links. Those links are the actual bussiness logic that we can call them TApps.

The TApps runs, there are two kinds of payments the business users need to pay. 

* The gas fee that pay to the miners who is host the runtime to execute your TApp. 
* The usage fee that pay to the developers who made the actors that you business logic calls.
  Very common that if the business TApp make this TApp used by so-called 'end user', then the End user would need to pay for the usage. In this case, it is always called 'in-app-purchase'

# Beyond Zapier, TEA connects Web3 and Web2

Zapier is a typical Web2 platform. But TEA can be a bridge between Web2 and Web3, because;

* TEA state maintainer can watch layer 1 blockchain events, that can be used as **Trigger**.
* TEA state maintainer can sign and call a layer 1 blockchain smart contract as a **Action**.
* TEA actors can be called from/to layer 1 blockchain smart contract as an **Oracle**. Inside the oracle, Web2 logic can be executed.

# Technical design

**The following contents are technical and supposed to be internal used.**

## Billing based on actor function calls

Instead of current *in_app_purchase*, we can have a pay_per_call billing model. Every actor can have a price per call. The TApp or end user that trigger a call to this funciton need to pay for the usage.

## IFT3 Factory, the dev tool

Similar to Zapier, we should have a TApp that builds other TApps. It should has graphic UI but zero coding. User can generate a business TApp. The business TApps is nothing but a scripts describe the workflow connects each  actor with condition. It is simply a IFTTT. Can we call ours IFT3?

## IFT3 loader

The output from the TApp factory is a IFT3 app. It is likely text based human readable scripts. We can make a IFT3 loader that load the IFT3 scripts and run inside TEA runtime. 

IFT3 loader is a TApp that takes the CID of an IFT3 apps as input. 

IFT3 loader can be scheduled to run as cron job. Some IFT3 apps need trigger that wait for some event constantly. 

# Business model

Both IFT3 Factory and IFT3 loader are paid app. Well they can be supported with subsidy by the DAO at the beginning. 

The actors (oracles) are not free to use. They are paid to call. 

Our dev team makes those actors and apps and get user pay to use.

We can also run TEA nodes to earn gas fee.

**Very important**: IFT3 can work for non-crypto business clients, it is totally fine for IFT3 to get **fiat income** from client directly. When IFT 3 pay TEA to the miners, we can do exchange. 
