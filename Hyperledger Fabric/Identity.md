
The different actors in a blockchain network include peers, orderers, client applications, administrators and more. Each of these actors — active elements inside or outside a network able to consume services — has a digital identity encapsulated in an X.509 digital certificate.

A digital identity furthermore has some additional attributes that Fabric uses to determine permissions, and it gives the union of an identity and the associated attributes a special name — **principal**. Principals are just like userIDs or groupIDs, but a little more flexible.

For an identity to be **verifiable**, it must come from a **trusted** authority. A [membership service provider](https://hyperledger-fabric.readthedocs.io/en/release-2.5/membership/membership.html) (MSP) is that trusted authority in Fabric. More specifically, an MSP is a component that defines the rules that govern the valid identities for this organization.

## PKIs

**A public key infrastructure (PKI) is a collection of internet technologies that provides secure communications in a network.** It’s PKI that puts the **S** in **HTTPS** — and if you’re reading this documentation on a web browser.

![[Certificate Authority.png]]

There are four key elements to PKI:

- **Digital Certificates**
- **Public and Private Keys**
- **[[Certificate Authorities]]**
- **Certificate Revocation Lists**
