# Find at least one Boostrap
The client need to have a hardcoded bootstrap nodes IP address. As long as more than one of those bootstrap nodes is alive, the client will contact to get a full list of nodes that this bootstrap node know about. Note, this bootstrap node doesn't need to know all alive hosting nodes, he will know a subset of the full alive hosting node, but it is large enough for regular use. Most likely a node will know other hosting nodes that have direct connect with self.

# Ask for a list of other hosting nodes
The client query this boostrap node. 
Request API ipaddress/list_known_hosts
Response list of know hosts. Every host will include:
- ip or url, http or https
- mechine_id. or tea_id this is also the public key of TPM

The client can randomly select any of the alive host as the server. The following communications are between client and this host.

# Client generate a symmtric key K1
Client generate a random K1. The following communication to the host will need this K1
This K1 store in the client memory only. 

# Client encrypt K1 using host tea_id
Client encrypt K1 using the known host tea_id, send the encrypted K1(enc_k1) to host.

# Host receive and decrypt back to k1. 
The K1 is decrypted and stored in memory of host. 
# Host generate session_id to refer to this client

The K1 is the value of a key_value_pair which session_id is the key.
# Host response a session_id to client
Host generate a random session_id and response to client. Client will use this client id for every communication later on.

# Client can send request using K1 to encrypt
Next time, when client send request to host, always
- attach Session_id to idendity itself
- use K1 to encrypt any confidential information. 

# Host decrypt request and response
Host can look up the hashmap key== session_id. value => K1.
Use K1 to decrypt the encrypted information. restore the plain text request
Handle request, get plain text resposne. 
Use k1 to encrypt response.
Response the encrypted response back to client.


# We do not need https, why?
Https assume the host (server) is honest, but tea project doesn't have such assumption. 
TEA Project assume the server is untrusted, only the enclave is trusted. 
As long as we have enclave level security, we do not care http or https. Since we have a higher confidential level than https.

# Use TLS?
This is a option. If we can find a TLS framework .


