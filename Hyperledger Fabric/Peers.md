A fundamental element of a Hyperledger Fabric blockchain network is the set of _peer nodes_ (or, simply, _peers_). Peers are fundamental because they manage ledgers and smart contracts.

![[Pasted image 20230820201812.png]]

*A Fabric blockchain network (above) is comprised of peers (non-ordering nodes), each of which stores and manages copies of ledgers and smart contracts. In this example, the Fabric network N consists of peers P1, P2 and P3, each of which maintains its own instance of the distributed ledger L1. P1, P2 and P3 each invoke the same chaincode, S1, to access their respective copies of the distributed ledger.*

## Ledgers and Chaincode

![[Pasted image 20230820202030.png]]
_A peer hosts instances of ledgers and instances of chaincodes (above). In this example, P1 hosts an instance of ledger L1 and an instance of chaincode S1. There can be many ledgers and chaincodes hosted on any individual peer._

### Multiple Ledgers

![[Pasted image 20230820202116.png]]
_A peer hosting multiple ledgers (above). Peers host one or more ledgers and the chaincodes that access them. In this example, peer P1 hosts ledgers L1 and L2. Ledger L1 is accessed using chaincode S1; Ledger L2 can be accessed using either chaincode S1 or S2._

### Applications and Peers

![[Applications and Peers]]

## Peers and Orderers

Refer [[Ordering Services]]
