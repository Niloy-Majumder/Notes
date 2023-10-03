
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
```

```bash
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

You can see the genesis block and chaneel pransaction in your nfc server under filestore pvc
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

```bash
helm install installchaincode -n initialpeerorg helm-charts/fabric-ops/ -f examples/fabric-ops/initialpeerorg/install-chaincode.yaml
```
> If different tar file is used change the yaml file accordingly

*Wait for some time. It takes quite a while*

# Deploy Org1 environment

