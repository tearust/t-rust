TApps are 3 tiers architect. [front_end](front_end.md)\], back end and [database](database.md) tiers.

The back end acts as its counter part web server in web2. but it more like the Lambda or Funciton-as-a-Service. 

The biggest difference for TEA project back end is that it doesn't require a specific IP address or domain name. There are many miners provide the [hosting_nodes](hosting_nodes.md) service. End user can connect to any of them to launch the same TApps, it should work exactly the same. 

For example, if TEA Party is a TApp that end user would like to login, its CID is cid_tea_party_12345. This user can visit http://one_of_host_nodes_ip/tapp/cid_tea_party_12345, or https://some_ssl_domain_name/tapp/cid_tea_party_12345.  Either way works. The cid_tea_party_12345 matters but the ip address doesn't.

The IPFS object poitns to cid_tea_party_12345 is the [front_end](front_end.md) of tea party app. After it is loaded into end user's browser, it will start to send web requests to this connected [hosting_nodes](hosting_nodes.md) and wait for response. This host will handle the request.

There are two types of request, [queies](queies.md) and [mutations](mutations.md).
