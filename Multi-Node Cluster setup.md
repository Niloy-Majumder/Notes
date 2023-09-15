Get the repo:
https://github.com/justmeandopensource/kubernetes.git

Navigate to Multi-master

`vagrant up`

Ssh to every node:

`vagrant ssh node` or `ssh root@node-ip`

### In Load Balancer :

`apt update && apt install -y haproxy`

Append the below lines to `/etc/haproxy/haproxy.cfg` :

`vi /etc/haproxy/haproxy.cfg`


```
frontend kubernetes-frontend
    bind 172.16.16.100:6443
    mode tcp
    option tcplog
    default_backend kubernetes-backend

backend kubernetes-backend
    mode tcp
    option tcp-check
    balance roundrobin
    server kmaster1 172.16.16.101:6443 check fall 3 rise 2
    server kmaster2 172.16.16.102:6443 check fall 3 rise 2
```


`systemctl restart haproxy`


###  On all kubernetes nodes (kmaster1, kmaster2, kworker1) :

##### Disable Firewall

`ufw disable`

##### Disable Swap

`swapoff -a; sed -i '/swap/d' /etc/fstab`

##### Update sysctl settings for Kubernetes networking

```
cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```

##### Install docker engine

```
{
  apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
  add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  apt update && apt install -y docker-ce=5:19.03.10~3-0~ubuntu-focal containerd.io
}
```

### Kubernetes Setup

##### Add Apt repository

```
{
  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
}
```

##### Install Kubernetes components

```
apt update && apt install -y kubeadm=1.27.0-00 kubelet=1.27.0-00 kubectl=1.27.0-00
```



## On any one of the Kubernetes master node (Eg: kmaster1)

##### Initialize Kubernetes Cluster

```
kubeadm init --control-plane-endpoint="172.16.16.100:6443" --upload-certs --apiserver-advertise-address=172.16.16.101 --pod-network-cidr=192.168.0.0/16
```

Copy the commands to join other master nodes and worker nodes.

##### Deploy Calico network

```
kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f https://docs.projectcalico.org/v3.15/manifests/calico.yaml
```


## Downloading kube config to your local machine

On your host machine

```bash
mkdir ~/.kube
scp root@172.16.16.101:/etc/kubernetes/admin.conf ~/.kube/config
```

To add roles to the Worker nodes:

`kubectl label node kworker1 node-role.kubernetes.io/worker=worker`

## Verifying the cluster

```bash
kubectl cluster-info
kubectl get nodes
kubectl get cs
```

# Troubleshooting:

If error occurs "Container runtime is not running" :

```bash
rm /etc/containerd/config.toml
systemctl restart containerd
``` 


If after deployment Component shows unhealthy then:

Modify the following files on all master nodes:

```bash
$ sudo vi /etc/kubernetes/manifests/kube-scheduler.yaml
```

Clear the line (spec->containers->command) containing this phrase: - --port=0

```bash
$ sudo vi /etc/kubernetes/manifests/kube-controller-manager.yaml
```

Clear the line (spec->containers->command) containing this phrase: - --port=0

```bash
$ sudo systemctl restart kubelet.service
```
