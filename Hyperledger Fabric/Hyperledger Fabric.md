
Hyperledger Fabric is an open source enterprise-grade permissioned distributed ledger technology (DLT) platform, designed for use in enterprise contexts, that delivers some key differentiating capabilities over other popular distributed ledger or blockchain platforms.

Fabric has a highly **<font color="#2DC26B">modular</font>** and **<font color="#2DC26B">configurable</font>** architecture, enabling innovation, versatility and optimization for a broad range of industry use cases including banking, finance, insurance, healthcare, human resources, supply chain and even digital music delivery.

The Fabric platform is also **[permissioned](obsidian://open?vault=Notes&file=Hyperledger%20Fabric%2FPermissioned%20vs%20Permissionless%20Blockchains)** ([[Permissioned vs Permissionless Blockchains]]), meaning that, unlike with a public permissionless network, the participants are known to each other, rather than anonymous and therefore fully untrusted. This means that while the participants may not _fully_ trust one another (they may, for example, be competitors in the same industry), a network can be operated under a governance model that is built off of what trust _does_ exist between participants

One of the most important of the platform’s differentiators is its support for **pluggable consensus protocols** that enable the platform to be more effectively customized to fit particular use cases and trust models.

---

## Modularity

At a high level, Fabric is comprised of the following modular components:

- A pluggable _ordering service_ establishes consensus on the order of transactions and then broadcasts blocks to peers.
- A pluggable _membership service provider_ is responsible for associating entities in the network with cryptographic identities.
- An optional _peer-to-peer gossip service_ disseminates the blocks output by ordering service to other peers.
- **[[Smart contracts]]** (”chaincode”) run within a container environment (e.g. Docker) for isolation. They can be written in standard programming languages but do not have direct access to the ledger state.
- The ledger can be configured to support a variety of DBMSs.
- A pluggable endorsement and validation policy enforcement that can be independently configured per application.

---

## Hyperledger Fabric Model

This section outlines the key design features woven into Hyperledger Fabric that fulfill its promise of a comprehensive, yet customizable, enterprise blockchain solution:

- **Assets** — Asset definitions enable the exchange of almost anything with monetary value over the network, from whole foods to antique cars to currency futures. Assets can be represented in binary and/or JSON form.
- **[Chaincode](obsidian://open?vault=Notes&file=Hyperledger%20Fabric%2FSmart%20contracts)** — Chaincode execution is partitioned from transaction ordering, limiting the required levels of trust and verification across node types, and optimizing network scalability and performance.
- **[[Ledger Features]]**— The immutable, shared ledger encodes the entire transaction history for each channel, and includes SQL-like query capability for efficient auditing and dispute resolution.
- **Privacy** — Channels and private data collections enable private and confidential multi-lateral transactions that are usually required by competing businesses and regulated industries that exchange assets on a common network.
- **Security & Membership Services**— Permissioned membership provides a trusted blockchain network, where participants know that all transactions can be detected and traced by authorized regulators and auditors.
- **Consensus**— A unique approach to consensus enables the flexibility and scalability needed for the enterprise.