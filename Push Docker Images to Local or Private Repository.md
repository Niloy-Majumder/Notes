
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

### Add IP to Name resolution

Edit the `/etc/hosts` file and add the following to let your PC know how to change name to IP address:

```
10.244.1.205    ipfsstagei.hyderabad.cdac.in
```
### Tag your Image first with the local registry:

```bash
docker tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]
```

Example: 

```bash
docker tag hyperledger/fabric-nodeenv:2.3   ipfsstagei.hyderabad.cdac.in:5000/hyperledger/fabric-nodeenv:2.3
```
>For latest images just don't need to specify the version number

Check the tag with `docker images` command

### Push the Image to your local Registry:

```bash
docker push ipfsstagei.hyderabad.cdac.in:5000/hyperledger/fabric-nodeenv:2.3
```

#### Check All the images in the catlog in browser:

Go to `http://10.244.1.205:5000/v2/_catalog` in the browser