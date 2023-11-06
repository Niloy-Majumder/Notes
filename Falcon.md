Follow [[Falcon-Prerequisites]] First
# Deploy Initial Orderers, Peers and Channel
## Deploy a Filestore server

*Update the StorageClass value in the examples/filestore/values.yaml accordingly*

Then: 

```bash
helm install filestore -n filestore helm-charts/filestore/ -f examples/filestore/values.yaml --create-namespace
```

Once deployed:

```bash
curl http://filestore.my-hlf-domain.com:30001
```

If you're getting the following response then that means your Ingress, Custom DNS etc are working properly:
![[Pasted image 20231003124756.png]]

## Deploy RootCA

```bash
kubectl create ns orderer
kubectl -n orderer create secret generic rca-secret --from-literal=user=rca-admin --from-literal=password=rcaComplexPassword
```

```bash
helm install root-ca -n orderer helm-charts/fabric-ca -f examples/fabric-ca/root-ca.yaml
```

To verify the server, you can get into any running pod in the cluster and send a curl request as below;

```bash
curl https://root-ca.my-hlf-domain.com:30000/cainfo --insecure
```

## Deploy TLSCA

```bash
kubectl -n orderer create secret generic tlsca-secret --from-literal=user=tlsca-admin --from-literal=password=tlscaComplexPassword
```

```bash
helm install tls-ca -n orderer helm-charts/fabric-ca -f examples/fabric-ca/tls-ca.yaml
```

## Create ROOTCA identities

> Note:- Every identity registration job must be executed in the same namespace where the respective CA's are running. And the admin credentials secret name must be supplied to the values file at `Values.ca.admin_secret`

```bash
helm install rootca-ops -n orderer helm-charts/fabric-ops/ -f examples/fabric-ops/rootca/rootca-identities.yaml
```

## Create TLSCA identities

```bash
helm install tlsca-ops -n orderer helm-charts/fabric-ops/ -f examples/fabric-ops/tlsca/tlsca-identities.yaml
```

## Deploy Orderer ICA

```bash
kubectl -n orderer create secret generic orderer-secret --from-literal=user=ica-orderer --from-literal=password=icaordererSamplePassword
```

```bash
helm install ica-orderer -n orderer helm-charts/fabric-ca -f examples/fabric-ca/ica-orderer.yaml
```

## Deploy Initial peer org ICA

```bash
kubectl create ns initialpeerorg
kubectl -n initialpeerorg create secret generic initialpeerorg-secret --from-literal=user=ica-initialpeerorg --from-literal=password=icainitialpeerorgSamplePassword
```

```bash
helm install ica-initialpeerorg -n initialpeerorg helm-charts/fabric-ca -f examples/fabric-ca/ica-initialpeerorg.yaml
```

## Create Orderer identities with ica-orderer

```bash
helm install orderer-ops -n orderer helm-charts/fabric-ops/ -f examples/fabric-ops/orderer/orderer-identities.yaml
```

## Create Initialpeerorg identities with ica-initialpeerorg

```bash
helm install initialpeerorg-ops -n initialpeerorg helm-charts/fabric-ops/ -f examples/fabric-ops/initialpeerorg/identities.yaml
```

## Generate Genesisblock & Channel transaction file

```bash
helm install cryptogen -n orderer helm-charts/fabric-ops/ -f examples/fabric-ops/orderer/orderer-cryptogen.yaml
```

You can see the genesis block and channel transaction in your nfs server under filestore pvc
## Deploy Orderer

```bash
helm install orderer -n orderer helm-charts/fabric-orderer/ -f examples/fabric-orderer/orderer.yaml
```

## Deploy Peers on Initial peer org

Change storageClass at `examples/fabric-peer/initialpeerorg/values.yaml`

```bash
helm install peer -n initialpeerorg helm-charts/fabric-peer/ -f examples/fabric-peer/initialpeerorg/values.yaml
```

*Wait a bit as it takes time. (Can be 10 - 15 minutes)*

## Create Channel

```bash
helm install channelcreate -n initialpeerorg helm-charts/fabric-ops/ -f examples/fabric-ops/initialpeerorg/channel-create.yaml
```

## Update Anchor peers of Initial peer org

```bash
helm install updateanchorpeer -n initialpeerorg helm-charts/fabric-ops/ -f examples/fabric-ops/initialpeerorg/update-anchor-peer.yaml
```

## Install chaincode on Initialpeerorg Peers

Before you install chaincode , you need to upload the packaged chaincode file to the filestore under your project directory. If you don't have any chaincode, you can upload the sample chaincode from our repository `examples/files/basic-chaincode_go_1.0.tar.gz` to `/usr/share/nginx/html/yourproject` path in the filestore server.

```bash
sudo cp examples/files/basic-chaincode_go_1.0.tar.gz  /nfs-vol/pvc-21cb9e89-e6db-4adb-9bbc-f8a57c02b465/yourproject/ 
```
> Change Accordingly
> Make sure you change the permissions also to -rw-rw-r-- to be able to get it via http call


```bash
helm install installchaincode -n initialpeerorg helm-charts/fabric-ops/ -f examples/fabric-ops/initialpeerorg/install-chaincode.yaml
```
> If different tar file is used change the yaml file accordingly

*Wait for some time. It takes quite a while*

# Deploy Org1 environment

## Deploy Org1 ICA

```bash
kubectl create namespace org1
kubectl -n org1 create secret generic org1-secret --from-literal=user=ica-org1 --from-literal=password=icaorg1SamplePassword
```

```bash
helm install ica-org1 -n org1 helm-charts/fabric-ca -f examples/fabric-ca/ica-org1.yaml
```

## Add Org1 to Network

Comment out the org2 section from the Values.organizatons array in the values file `examples/fabric-ops/initialpeerorg/configure-org-channel.yaml` for now since we have not deployed the Org2 yet.

```bash
vim examples/fabric-ops/initialpeerorg/configure-org-channel.yaml
```

```bash
helm install configorgchannel -n initialpeerorg helm-charts/fabric-ops/ -f examples/fabric-ops/initialpeerorg/configure-org-channel.yaml
```

## Create Org1 Identities with ica-org1

```bash
helm install org1-ca-ops -n org1 helm-charts/fabric-ops/ -f examples/fabric-ops/org1/identities.yaml 
```

## Deploy Peers on Org1

Change storage class on `examples/fabric-peer/org1/values.yaml`

```bash
helm install peer -n org1 helm-charts/fabric-peer/ -f examples/fabric-peer/org1/values.yaml
```

*Wait for some time. It takes quite a while*

## Install Chaincode on Peers

```bash
helm install installchaincode -n org1 helm-charts/fabric-ops/ -f examples/fabric-ops/org1/install-chaincode.yaml
```

*Wait for some time. It takes quite a while*

## Update Anchor Peer on Org1

```bash
helm install updateanchorpeer -n org1 helm-charts/fabric-ops/ -f examples/fabric-ops/org1/update-anchor-peer.yaml
```

# Deploy Org2 environment

## Deploy Org2 ICA

```bash
kubectl create namespace org2
kubectl -n org2 create secret generic org2-secret --from-literal=user=ica-org2 --from-literal=password=icaorg2SamplePassword
```

```bash
helm install ica-org2 -n org2 helm-charts/fabric-ca -f examples/fabric-ca/ica-org2.yaml
```

## Add Org2 to Network

Comment out the org1 section from the Values.organizatons array in the values file `examples/fabric-ops/initialpeerorg/configure-org-channel.yaml` 

```bash
vim examples/fabric-ops/initialpeerorg/configure-org-channel.yaml
```

```bash
helm upgrade configorgchannel -n initialpeerorg helm-charts/fabric-ops/ -f examples/fabric-ops/initialpeerorg/configure-org-channel.yaml
``````

## Create Org2 Identities with ica-org1

```bash
helm install org2-ca-ops -n org2 helm-charts/fabric-ops/ -f examples/fabric-ops/org2/identities.yaml 
```
## Deploy Peers on Org2 

Change storage class on `examples/fabric-peer/org2/values.yaml`

```bash
helm install peer -n org2 helm-charts/fabric-peer/ -f examples/fabric-peer/org2/values.yaml
```

*Wait for some time. It takes quite a while*

## Install Chaincode on Peers

```bash
helm install installchaincode -n org2 helm-charts/fabric-ops/ -f examples/fabric-ops/org2/install-chaincode.yaml
```

*Wait for some time. It takes quite a while*

## Update Anchor Peer on Org2

```bash
helm install updateanchorpeer -n org2 helm-charts/fabric-ops/ -f examples/fabric-ops/org2/update-anchor-peer.yaml
```


# Approve & Commit Chain Code

> Before approving chaincode deploy [[#Deploy Fabric CLI]] on each org and use `peer lifecycle chaincode queryinstalled` to check for Package_Id and change the value accordingly in the values file for approvechaincode



Move the collection-config to nfs server filestore->your_project 
## Approve Chaincode on InitialPeerOrg

```bash
helm install approvechaincode -n initialpeerorg helm-charts/fabric-ops/ -f examples/fabric-ops/initialpeerorg/approve-chaincode.yaml
```
> Change values file accordingly if you have changed the chaoincode package

## Approve Chaincode on Org1

```bash
helm install approvechaincode -n org1 helm-charts/fabric-ops/ -f examples/fabric-ops/org1/approve-chaincode.yaml
```

## Approve Chaincode on Org2

```bash
helm install approvechaincode -n org2 helm-charts/fabric-ops/ -f examples/fabric-ops/org2/approve-chaincode.yaml
```

## Commit Chaincode on InitialPeerOrg

```bash
helm install commitchaincode -n initialpeerorg helm-charts/fabric-ops/ -f examples/fabric-ops/initialpeerorg/commit-chaincode.yaml
```

# Deploy Fabric CLI

Fabric cli shoul be deployed per org:

```bash
helm install cli-tools -n initialpeerorg helm-charts/fabric-tools/ -f examples/fabric-ops/initialpeerorg/identities.yaml
```

### To run inside cli pod

```bash
 kubectl exec -i -t -n initialpeerorg cli-tools-initialpeerorg-5c69487c5b-cwr8f -- peer chaincode query -C mychannel -n basic-chaincode-v1 -c '{"Args":["GetAllAssets"]}'
```

# Errors

```
Error: chaincode install failed with status: 500 - failed to invoke backing implementation of 'InstallChaincode': could not build chaincode: docker build failed: docker image build failed: Post "http://localhost:2375/v1.25/build?networkmode=host&t=dev-peer0-initialpeerorg-fabcar_1.0-2c93a68da6d155d41ac2138a1457f1b948a2feb8c632915a65711cbb7afef4d2-14137c7f831f70a39437547ef5dfbc05a7f59f4b8b47fe5e336680c6ef5cc4ec": docker build failed: Failed to pull hyperledger/fabric-nodeenv:2.4: API error (503): error creating temporary lease: transport is closing: unavailable
```