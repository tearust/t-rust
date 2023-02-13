# Where are Messages Stored?

In the TEA Party, the messages are stored in an [OrbitDB](http://orbitdb.org) database.
OrbitDB is a non-relational database running on top of IPFS.

## Security

For TEA Party's public messages, there's no need to encrypt.

Messages are stored in [OrbitDb](../z_glossary/OrbitDb.md) (and eventually in IPFS) in plain text.

But private messages aren't saved in plain text. The hosting nodes will encrypt the message using the [App_AES_Key](../z_glossary/App_AES_Key.md) and then save the cypher to OrbitDB. 

Because only TEA Party hosting nodes have the [App_AES_Key](../z_glossary/App_AES_Key.md) in their own enclave, other apps or users cannot get the content of these messages.

## Cost

OrbitDB /IPFS is very cheap compared to using the [state machine](../z_glossary/State_Machine.md) which exclusively uses RAM to store the state. For more details, please see [OrbitDb](../z_glossary/OrbitDb.md).
