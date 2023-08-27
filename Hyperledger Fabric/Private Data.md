
## What is a private data collection?

A collection is the combination of two elements:

1. **The actual private data**, sent peer-to-peer [via gossip protocol](https://hyperledger-fabric.readthedocs.io/en/release-2.5/gossip.html) to only the organization(s) authorized to see it. This data is stored in a private state database on the peers of authorized organizations, which can be accessed from chaincode on these authorized peers. The ordering service is not involved here and does not see the private data.
2. **A hash of that data**, which is endorsed, ordered, and written to the ledgers of every peer on the channel. The hash serves as evidence of the transaction and is used for state validation and can be used for audit purposes.


![[Pasted image 20230827162013.png]]