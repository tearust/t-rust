# Front-end
The TEA Project isn't a front-end framework. Developers can use whatever front-end tech stack they're most comfortable with.

For every TApp, the front-end code could be comprised of JS/HTML/CSS code that's stored in IPFS (or another type of decentralized storage). The CID (Content ID, actually the hash of the content) is the key to retrieving the content.

When the end user loads a TApp, it actually loads the static resources refereced by the CID from one of the IPFS nodes.

The front-end must be a **static** resource. All dymanic content will be queried from the [hosting_CML](hosting_CML.md). 
