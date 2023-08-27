Many distributed blockchains, such as Ethereum and Bitcoin, are not permissioned, which means that any node can participate in the consensus process, wherein transactions are ordered and bundled into blocks.

Hyperledger Fabric works differently. It features a node called an **orderer** (it’s also known as an “ordering node”) that does this transaction ordering, which along with other orderer nodes forms an **ordering service**. Because Fabric’s design relies on **deterministic** consensus algorithms, any block validated by the peer is guaranteed to be final and correct.

## Orderers and the transaction flow

### Phase 1: Transaction Proposal

Phase 1 of the transaction workflow involves an interaction between a client application, peers and orderers. In Phase 1, the client application initiates a request to the Fabric Gateway service to evaluate a transaction proposal.

The target peer, selected by the client application, executes the transaction by invoking chaincode — this step can be described as simulating the transaction, because it runs the transaction without any effect on the ledger. The peer then returns its transaction result to the client.

The gateway service also forwards the transaction proposal to the required _endorsing peers_ (based on the endorsement policies), which also execute the transaction and return their results to the peer. The gateway service collects all responses, and if they collectively satisfy the endorsement policies, forwards the transaction to the ordering service.

If the proposed transaction would write to a private data collection, (as _transient_ data) the client application must explicitly specify the organizations required for endorsement.

Note that a peer endorses a proposal response by adding its digital signature, and signing the entire payload using its private key. This endorsement can be subsequently used to prove that this organization’s peer generated a particular response.

### Phase 2: Transaction Ordering

The second phase of the transaction workflow is the ordering and packaging phase. The ordering service (running on orderer nodes) receives transactions containing signed and endorsed proposal responses, from one or more applications via the gateway service, and orders and packages the transactions into blocks. These are the blocks (which are also ordered) — consisting of endorsed and ordered transactions — that make up a Fabric blockchain ledger.

For details about the ordering and packaging phase, see the [conceptual information about the ordering phase](https://hyperledger-fabric.readthedocs.io/en/release-2.5/orderer/ordering_service.html#phase-two-ordering-and-packaging-transactions-into-blocks).

### Phase 3: Validation and Commitment

The third and final phase of the transaction workflow is the distribution of ordered transactions from orderers to peers. Each peer then validates each transaction, in the correct order, and ensures that each transaction has been consistently endorsed by all required organizations. Only then does the peer commit the block to its copy of the channel ledger.

![[Pasted image 20230820212419.png]]

_An orderer node distributes ordered blocks to peers for validation and commitment. In this example above, orderer O1 distributes block B2 to peers P1 and P2. Peer P1 processes block B2, resulting in a new block being added to ledger L1 on P1. In parallel, peer P2 processes block B2, resulting in a new block being added to ledger L1 on P2. Once complete, the ledger L1 has been consistently updated on peers P1 and P2, and the gateway service informs the relevant applications that the transaction has been committed._


## Ordering service implementations

While every ordering service currently available handles transactions and configuration updates the same way, there are nevertheless several different implementations for achieving consensus on the strict ordering of transactions between ordering service nodes.

- **[[Raft]]** (recommended)

- **Kafka** (deprecated in v2.x)
   
- **Solo** (deprecated in v2.x)
