
A client application connects to the [Fabric Gateway](https://hyperledger-fabric.readthedocs.io/en/release-2.5/gateway.html) service on a peer in order to access ledgers and chaincodes. Through a peer connection on the gateway, applications can run chaincodes to query or update the ledger. The result of a ledger query transaction is returned with simple processing, whereas a ledger update (write) involves a more complex workflow between applications, peers and orderers.

### Phase 1 - Transaction Proposal and Endorsement

Phase 1 of a ledger update (write) consists of transaction proposal submission, execution and endorsement:

a) **Transaction proposal** — The client application (A1) submits a signed transaction proposal by connecting to the gateway service on P1. A1 must either delegate the selection of endorsing organizations to the gateway service or explicitly identify the organizations required for endorsement.

b) **Transaction execution** — The gateway service selects P1, or another peer in its organization, to execute the transaction. The selected peer executes the chaincode (S1) specified in the proposal, generates a proposal response (containing the read-write set). The selected peer signs the proposal response and returns it to the gateway.

c) **Transaction endorsement** — The gateway repeats transaction execution (b) for each organization required by the chaincode (smart contract) endorsement policies. The gateway service collects the signed proposal responses and creates a transaction envelope — which it returns to the client (SDK) for signing.

### Phase 2 - Transaction Submission and Ordering

Phase 2 of a ledger update consists of transaction submission and ordering into blocks:

a) **Transaction submission** — The client (SDK) sends the signed transaction envelope to the gateway service. The gateway forwards the envelope to an ordering node and returns a success message to the client.

b) **Transaction ordering** — The ordering node (O1) verifies the signature, and the ordering service orders the transaction, and packages it with other ordered transactions into blocks. The ordering service then distributes the block to all peers in the channel for validation and commitment to the ledger.

### Phase 3 - Transaction Validation and Commitment

Phase 3 of a ledger update consists of transaction validation, ledger commitment and a commit event:

a) **Transaction validation** — Each peer checks that the client signature on the transaction envelope matches the signature on the original transaction proposal. Each peer also checks that all read-write sets and status responses are equivalent (i.e. the endorsements from all peers match) and that the endorsements satisfy the endorsement policies. Each peer then marks each transaction as valid or invalid for commitment to the ledger.

b) **Transaction commitment** — Each peer commits the ordered block of transactions to the channel ledger (L1). The commit is an immutable ledger update (write) to the channel ledger. The world state (essentially, the sum of all valid transactions) of the channel is updated with results of valid transactions only.

c) **Commit event** — Each peer that commits to the ledger sends the client a commit status event with proof of the ledger update.
