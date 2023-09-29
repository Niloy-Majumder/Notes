### Install Helm 

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

### Nginx ingress

> Check Kubernetes and Nginx version compatibility at https://github.com/kubernetes/ingress-nginx

#### Download the Nginx Ingress Yaml File:

```bash
curl https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.9.0/deploy/static/provider/baremetal/deploy.yaml > nginx-ingress
```

To have specific Nodeports append nodePort to the NodePort service Section:

```yaml
apiVersion: v1
kind: Service
...
spec:
  ...
  ports:
  - appProtocol: http
    name: http
    port: 80
    protocol: TCP
    targetPort: http
    nodePort: 30001
  - appProtocol: https
    name: https
    port: 443
    protocol: TCP
    targetPort: https
    nodePort: 30000
...
  type: NodePort
```

#### Apply Nginx Ingress :

```bash
kubectl apply -f nginx-ingress
```

#### Enable ssl passthrough:

>Use lens or something to update the deployment yaml Or change it in the nginx-ingress.yaml file and re-apply

You’re looking for `_— enable-ssl-passthrough`_ to be passed through to the controller as an argument:

```yaml
apiVersion: apps/v1  
kind: Deployment  
...  
spec:  
  template:  
    spec:  
      containers:  
      - args:  
          ...  
          - --enable-ssl-passthrough
```


### To add Custom DNS

Edit CoreDNS Config Map:

```bash
kubectl -n kube-system edit configmap coredns
```

Sample:
```yaml
apiVersion: v1
data:
  Corefile: |
    .:53 {
        errors
        health {
           lameduck 5s
        }
        ready
        rewrite name foo.example.com nginx-service.default.svc.cluster.local
        kubernetes cluster.local in-addr.arpa ip6.arpa {
           pods insecure
           fallthrough in-addr.arpa ip6.arpa
           ttl 30
        }
        prometheus :9153
        forward . /etc/resolv.conf {
           max_concurrent 1000
        }
        cache 30
        loop
        reload
        loadbalance
    }
```

To let CoreDNS know that config file has changed gracefully:

```bash
kubectl exec -n kube-system coredns-980047985-g2748 -- kill -SIGUSR1 1
```

==Important==
> Usually this command will not work as we cannot get a shell of any kube-system pods, so try restarting(evicting) the pods if that is an option.


Check if it is working. Example:
```bash
$ kubectl run -it --rm --restart=Never --image=infoblox/dnstools:latest dnstools If you dont see a command prompt, try pressing enter. 
/ # host foo foo.default.svc.cluster.local has address 10.0.0.72 
/ # host foo.example.com foo.example.com has address 10.0.0.72 
/ # host bar.example.com Host bar.example.com not found: 3(NXDOMAIN) 
/ #
```


To Add Custom DNS Zone:

Create file plugin in configmap and add dnszone configuration:

>Change Ips according to your Worker nodes
```yaml
apiVersion: v1
data:
  Corefile: |
    .:53 {
        errors
        health {
           lameduck 5s
        }
        ready
        rewrite name foo.example.com nginx-service.default.svc.cluster.local
        kubernetes cluster.local in-addr.arpa ip6.arpa {
           pods insecure
           fallthrough in-addr.arpa ip6.arpa
           ttl 30
        }
        file /etc/coredns/example.db example.org
        file /etc/coredns/my-hlf-domain.db my-hlf-domain.com
        prometheus :9153
        forward . /etc/resolv.conf {
           max_concurrent 1000
        }
        cache 30
        loop
        reload
        loadbalance
    }
  example.db: |
    ; example.org test file
    example.org.            IN      SOA     sns.dns.icann.org. noc.dns.icann.org. 2015082541 7200 3600 1209600 3600
    example.org.            IN      NS      b.iana-servers.net.
    example.org.            IN      NS      a.iana-servers.net.
    example.org.            IN      A       127.0.0.1
    a.b.c.w.example.org.    IN      TXT     "Not a wildcard"
    cname.example.org.      IN      CNAME   www.example.net.

    service.example.org.    IN      SRV     8080 10 10 example.org.
  my-hlf-domain.db: |
    ; my-hlf-domain test file
    my-hlf-domain.com.            IN      SOA     sns.dns.icann.org. noc.dns.icann.org. 2015082541 7200 3600 1209600 3600
    my-hlf-domain.com.            IN      NS      b.iana-servers.net.
    my-hlf-domain.com.            IN      NS      a.iana-servers.net.
    *.my-hlf-domain.com.          IN      A       10.166.239.131
    *.my-hlf-domain.com.          IN      A       10.166.239.214
    a.b.c.w.my-hlf-domain.com.    IN      TXT     "Not a wildcard"
    cname.my-hlf-domain.com.      IN      CNAME   www.my-hlf-domain.net.

    service.my-hlf-domain.com.    IN      SRV     8080 10 10 my-hlf-domain.com.
kind: ConfigMap

```

> This will register my-hlf-domain.com dns zone with coredns. 
> Be sure to restart the deployment

> But if you try to do `telnet anyname.my-hlf-domin.com 30000` it will not resolve because we have to tell Ubuntu to use coredns to resolve the names

Find CoreDns' IP :

```bash
kubectl get services --all-namespaces -o wide
```

Edit the `/etc/resolv.conf` File:

```bash
nameserver 10.0.0.10   # Replace with the actual CoreDNS service IP
```

Test DNS Resolution: 

```bash
nslookup example.com
```

*If this doesn't Work, Try*:

Edit `/etc/systemd/resolved.conf` file:

```bash
[Resolve]
DNS=10.0.0.10   # Replace with the actual CoreDNS service IP
```

After making changes, restart the systemd-resolved service:

```bash
sudo systemctl restart systemd-resolved
```

Now try Nslookup:

```bash
nslookup anyname.my-hlf-domain.com
```

Or 

```bash
telnet anyname.my-hlf-domain.com 30000
```

Or 

```bash
curl http://anyname.my-hlf-domain.com:30001
```

