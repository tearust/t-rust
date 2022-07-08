Reference page https://www.coindailynews.io/2022/05/11/aleph-im-aleph-review-all-you-need-to-know/

# Common features
Both projects trying to solve the similiar project, and claim to be decentralized cloud computing.

Notable common features:
- Decentralized cloud
- Cross chain layer2, blockchain (layer 1) agnostic
- High speed and low cost

# Different technical approach
## hardware enclave
TEA Project uses hardware security. Including TPM, GPS timestamp to reach new kind of consensus between nodes.

Aleph doesn't have such consensus. It is still a enhanced blockchain. 

TEA Project does not trust Docker VM becuase human can still login to the node and control the system may cause privacy breach.  TEA Project uses hardware enclave so that human has no control over the hardware enclave.

Aleph so far does not have enclave. Aleph claim a future improvement using hardware enclave. So far, security should be a major concern since the miners may be able to access the machine.

## Consensus
TEA Project is not a blockchain. It uses blockchain as layer1, one of the three roots of trust. It is more likely to be considered a GPS timestamp-based distributed database. There is no block in layer2, and no roll up back to layer1. 

Aleph is a traditional layer2. It can run code in layer2 VM, but still need to "roll up" to layer1. There is still blocks. Aleph consensus is still blockchain-based consensus. 

