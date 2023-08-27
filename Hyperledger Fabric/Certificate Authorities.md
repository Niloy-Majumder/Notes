Certificate Authorities dispenses X.509 certificates that can be used to identify components as belonging to an organization. Certificates issued by CAs can also be used to sign transactions to indicate that an organization endorses the transaction result.

Firstly, different components of the blockchain network use certificates to identify themselves to each other as being from a particular organization. That’s why there is usually more than one CA supporting a blockchain network – different organizations often use different CAs.

The mapping of certificates to member organizations is achieved via a structure called a [Membership Services Provider (MSP)](https://hyperledger-fabric.readthedocs.io/en/release-2.5/membership/membership.html), which defines an organization by creating an MSP which is tied to a root CA certificate to identify that components and identities were created by the root CA.

Secondly, CAs are at the heart of the transaction generation and validation process. Specifically, X.509 certificates are used in client application transaction proposals and smart contract transaction responses to digitally sign transactions.

### Root CAs, Intermediate CAs and Chains of Trust

CAs come in two flavors: **Root CAs** and **Intermediate CAs**. Because Root CAs have to **securely distribute** hundreds of millions of certificates to internet users, it makes sense to spread this process out across what are called _Intermediate CAs_. These Intermediate CAs have their certificates issued by the root CA or another intermediate authority, allowing the establishment of a “chain of trust” for any certificate that is issued by any CA in the chain.

![[Pasted image 20230820185825.png]]

### Fabric CA

It’s because CAs are so important that Fabric provides a built-in CA component to allow you to create CAs in the blockchain networks you form. This component — known as **Fabric CA** is a private root CA provider capable of managing digital identities of Fabric participants that have the form of X.509 certificates.

## Certificate Revocation Lists

A Certificate Revocation List (CRL) is easy to understand — it’s just a list of references to certificates that a CA knows to be revoked for one reason or another. If you recall the store scenario, a CRL would be like a list of stolen credit cards.