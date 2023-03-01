# Where Messages are Stored
In the TEA Party, the messages are stored in an [OrbitDB](http://orbitdb.org) database. OrbitDB is a non-relational database running on top of IPFS.

## Security
For TEA Party's public messages, there's no need to encrypt.

Messages are stored in [orbitdb](orbitdb.md) (and eventually in IPFS) in plain text.

But private messages aren't saved in plain text. The hosting nodes will encrypt the message using the [app_aes_key](app_aes_key.md) and then save the cypher to OrbitDB. 

Because only TEA Party hosting nodes have the [app_aes_key](app_aes_key.md) in their own enclave, other apps or users cannot get the content of these messages.

## Cost
OrbitDB /IPFS is very cheap compared to using the [state machine](state_machine.md) which exclusively uses RAM to store the state. For more details, please see [orbitdb](orbitdb.md).


