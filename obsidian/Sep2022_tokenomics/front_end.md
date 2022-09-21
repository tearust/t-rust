TApp front end runs inside end user's browser or mobile phone.

Use web app has an example. The front end is a set of Single Page Applicaiton (SPA) code (js, html, css). 

When developer deploying the TApp, it is just upload the front end code and backend functions compiled into wasm modules (we call them actors) to TEA project IPFS network.Then regiest the CID to the TAppStore. The CID of the front end would be the ID of this application. 

End user can load this application as long as he know this CID, and any of the [[hosting_nodes]] base URL.