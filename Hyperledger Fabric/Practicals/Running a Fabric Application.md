
![[Start the network]]
![[Creating a channel]]

--(Use CA)

### Deploy the smart contract

```bash
./network.sh deployCC -ccn basic -ccp ../asset-transfer-basic/chaincode-typescript/ -ccl typescript
```

### Prepare the sample application

```bash
cd asset-transfer-basic/application-gateway-typescript
npm install
```

## Run the sample application

```bash
npm start
```


*-(The error is meant for us to see how errors are handled)*
