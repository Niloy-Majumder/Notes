A blockchain network is a technical infrastructure that provides ledger and smart contract (which are packaged as part of a “chaincode”) services to applications. Primarily, smart contracts are used to generate transactions which are subsequently distributed to every peer node in the network where they are immutably recorded on their copy of the ledger. The users of applications might be end users using client applications or blockchain network administrators.

### Here’s a diagram representing the **final state** of our sample network.


![[sample network diagram.png]]

Three organizations, R1, R2, and R0 have jointly decided that they will establish a network. This network has a configuration, CC1, which all of the organizations have agreed to and which lists the definition of the organizations as well as the policies which define the roles each organization will play on the channel.

## Creating the network

The first step in creating a network or a channel is to agree to and then define its configuration:

The channel configuration, CC1, has been agreed to by organizations R1, R2, and R0 and is contained in a block known as a “configuration block” that is, typically, created by the `configtxgen` tool from a `configtx.yaml` file.

Once a configuration block exists, a channel can be said to **logically exist**, even though no components are physically joined to it. This configuration block contains a record of the organizations that can join components and interact on the channel, as well as the **[[Policies]]** that define the structure for how decisions are made and specific outcomes are reached.


## Certificate Authorities

![[Certificate Authorities]]

## Join nodes to the channel


- [[Peers]] are a fundamental element of the network because they host ledgers and chaincode (which contain smart contracts) and are therefore one of the physical points at which organizations that transact on a channel connect to the channel (the other being an application). A peer can belong to as many channels as an organizations deems appropriate.

- The [[Ordering Services]], on the other hand, gathers endorsed transactions from applications and orders them into transaction blocks, which are subsequently distributed to every peer node in the channel.

## Install, approve, and commit a chaincode

Chaincodes are installed on peers, and then defined and committed on a channel.

In Fabric, the business logic that defines how peer organizations interact with the ledger is contained in [[Smart Contracts]]. The structure that contains the smart contract, called chaincode, is installed on the relevant peers, approved by the relevant peer organizations, and committed on the channel. In this way, you can consider a chaincode to be **physically hosted** on a peer but **logically hosted** on a channel. For more information, check out [[Fabric chaincode lifecycle]]

The most important piece of information supplied within the chaincode definition is the **endorsement policy** . It describes which organizations must endorse transactions before they will be accepted by other organizations onto their copy of the ledger.

## Using an application on the channel

After a smart contract has been committed, client applications can be used to invoke transactions on a chaincode, via the Fabric Gateway service (the gateway). Just like peers and orderers, a client application has an identity that associates it with an organization.
