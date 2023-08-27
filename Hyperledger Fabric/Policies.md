At its most basic level, a policy is a set of rules that define the structure for how decisions are made and specific outcomes are reached. To that end, policies typically describe a **who** and a **what**, such as the access or rights that an individual has over an **asset**.

Policies are one of the things that make Hyperledger Fabric different from other blockchains like Ethereum or Bitcoin. In those systems, transactions can be generated and validated by any node in the network. The policies that govern the network are fixed at any point in time and can only be changed using the same process that governs the code.

## Policies Implementation

Policies are defined within the relevant administrative domain of a particular action defined by the policy. For example, the policy for adding a peer organization to a channel is defined within the administrative domain of the peer organizations (known as the `Application` group). Similarly, adding ordering nodes in the consenter set of the channel is controlled by a policy inside the `Orderer` group. Actions that cross both the peer and orderer organizational domains are contained in the `Channel` group.

### Smart contract endorsement policies

Every smart contract inside a chaincode package has an endorsement policy that specifies how many peers belonging to different channel members need to execute and validate a transaction against a given smart contract in order for the transaction to be considered valid. Hence, the endorsement policies define the organizations (through their peers) who must “endorse” (i.e., approve of) the execution of a proposal.

### Modification policies

There is one last type of policy that is crucial to how policies work in Fabric, the `Modification policy`. Modification policies specify the group of identities required to sign (approve) any configuration _update_. It is the policy that defines how the policy is updated. Thus, each channel configuration element includes a reference to a policy which governs its modification.

## How do you write a policy in Fabric

If you want to change anything in Fabric, the policy associated with the resource describes **who** needs to approve it, either with an explicit sign off from individuals, or an implicit sign off by a group. In Hyperledger Fabric, explicit sign offs in policies are expressed using the `Signature` syntax and implicit sign offs use the `ImplicitMeta` syntax.

### Signature policies

`Signature` policies define specific types of users who must sign in order for a policy to be satisfied such as `OR('Org1.peer', 'Org2.peer')`.The syntax supports arbitrary combinations of `AND`, `OR` and `NOutOf`. For example, a policy can be easily expressed by using `AND('Org1.member', 'Org2.member')`.

### ImplicitMeta policies

`ImplicitMeta` policies are only valid in the context of channel configuration which is based on a tiered hierarchy of policies in a configuration tree. ImplicitMeta policies aggregate the result of policies deeper in the configuration tree that are ultimately defined by Signature policies.

![[Pasted image 20230820201434.png]]

Here's a tabular comparison between signature policies and ImplicitMeta policies in Hyperledger Fabric:

| Aspect                   | Signature Policy                      | ImplicitMeta Policy                   |
|--------------------------|---------------------------------------|---------------------------------------|
| Purpose                  | Specifies required signatures         | References and combines other policies|
| Mechanism                | Logical expression using AND/OR       | References and defines logical operations|
| Customization            | Allows fine-grained endorsement logic | Simplifies policy management          |
| Use Cases                | Specific signature combinations       | Inheriting policies from higher levels|
| Direct vs. Referential   | Directly defines required signatures  | References and combines existing policies|
| Complexity               | More complex due to custom expressions| Simpler due to predefined policy combinations|
| Implementation in Chaincode| Enforced within chaincode logic     | Not implemented in chaincode, used for policy management|

Remember that both types of policies are essential in different contexts and are used based on the requirements of the blockchain network. Signature policies define precise endorsement requirements, while ImplicitMeta policies allow more streamlined policy management.


