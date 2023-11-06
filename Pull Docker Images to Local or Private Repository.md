
### Add the Configuration to pull from insecure local registry in docker-daemon

Add the following configuration to `/etc/docker/daemon.json`

```json
{
  "insecure-registries" : ["ipfsstagei.hyderabad.cdac.in:5000", "10.244.1.205:5000"]
}
```

And then restart docker

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```


### Pull the Image from your local Registry:

```bash
docker pull ipfsstagei.hyderabad.cdac.in:5000/hyperledger/fabric-nodeenv:2.3
```

> If `ipfsstagei.hyderabad.cdac.in` is not getting resolved then  edit `/etc/hosts` file and add the following:

```
10.244.1.205 ipfsstagei.hyderabad.cdac.in
```
