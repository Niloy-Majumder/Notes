Use the Vagrant file to spin up One Master VM and Two Worker VM:

> https://github.com/justmeandopensource/kubernetes.git

## On All Nodes:

### Execute the following commands on **all the nodes** for IPtables to see bridged traffic.

```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
```

```
sudo modprobe overlay
sudo modprobe br_netfilter
```

```
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF
```

```
sudo sysctl --system
```

### Disable swap on all the Nodes

```
sudo swapoff -a
(crontab -l 2>/dev/null; echo "@reboot /sbin/swapoff -a") | crontab - || true
```

### Install CRI-O Runtime On All The Nodes

>You can have any one of the following container runtimes.
>1. CRI-O
>2. containerd
>3. Docker Engine (using cri-dockerd)

Create the `.conf` file to load the modules at bootup

```

cat <<EOF | sudo tee /etc/modules-load.d/crio.conf
overlay
br_netfilter
EOF
```

```
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
```

```
sudo modprobe overlay 
sudo modprobe br_netfilter
```

```
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf net.bridge.bridge-nf-call-iptables = 1 net.ipv4.ip_forward = 1 net.bridge.bridge-nf-call-ip6tables = 1 EOF
```

```
sudo sysctl --system
```

Enable **cri-o** repositories for version 1.23

```
OS="xUbuntu_20.04"
VERSION="1.23"
```

```
cat <<EOF | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/ /
EOF
```

```
cat <<EOF | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:$VERSION.list deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/$VERSION/$OS/ / 
EOF
```

Add the gpg keys.

```
curl -L https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:$VERSION/$OS/Release.key | sudo apt-key --keyring /etc/apt/trusted.gpg.d/libcontainers.gpg add - 
```

```
curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/Release.key | sudo apt-key --keyring /etc/apt/trusted.gpg.d/libcontainers.gpg add -
```

Update and install crio and crio-tools.

```
sudo apt-get update 
sudo apt-get install cri-o cri-o-runc cri-tools -y
```

```
sudo systemctl daemon-reload 
sudo systemctl enable crio --now
```


### Install Kubeadm & Kubelet & Kubectl on all Nodes

```
sudo apt-get update 
sudo apt-get install -y apt-transport-https ca-certificates curl
```

Download the signing key

```
sudo curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
```

Add the GPG key and apt repository.

```
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Update apt repo

```
sudo apt-get update -y
```

Install Kubeadm & Kubelet & Kubectl

```
sudo apt-get install -y kubelet=1.27.3-00 kubectl=1.27.3-00 kubeadm=1.27.3-00
```

Add hold to the packages to prevent upgrades.

```
sudo apt-mark hold kubelet kubeadm kubectl
```

Add the node IP to `KUBELET_EXTRA_ARGS`.

```
sudo apt-get install -y jq
local_ip="$(ip --json a s | jq -r '.[] | if .ifname == "eth1" then .addr_info[] | if .family == "inet" then .local else empty end else empty end')"
```

==*Important*==
> Check if local_ip is set by `echo $local_ip`, if not then set manually your nodes' IP address.

```
cat > /etc/default/kubelet << EOF
KUBELET_EXTRA_ARGS=--node-ip=$local_ip
EOF
```

## Initialize Kubeadm On Master Node To Setup Control Plane

### Initialize the Cluster

Set the following environment variables. Replace `10.0.0.10` with the IP of your master node.

```
IPADDR="10.0.0.10"
NODENAME=$(hostname -s)
POD_CIDR="192.168.0.0/16"
```

```
sudo kubeadm init **--apiserver-advertise-address**=$IPADDR --apiserver-cert-extra-sans=$IPADDR --pod-network-cidr=$POD_CIDR --node-name $NODENAME --ignore-preflight-errors Swap
```

Copy the commands to join other master nodes and worker nodes.

Verify the kubeconfig by executing the following kubectl command tolist all the pods in the `kube-system` namespace.

```
kubectl get po -n kube-system
```

You verify all the cluster component health statuses using the following command.

```
kubectl get --raw='/readyz?verbose'
```

You can get the cluster info using the following command.

```
kubectl cluster-info 
```

By default, apps won’t get scheduled on the master node. If you want to use the master node for scheduling apps, taint the master node.

```
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

### Install Calico Network Plugin for Pod Networking

```
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
```

### Join Worker Nodes To Kubernetes Master Node

Join the worker node to the master node using the Kubeadm join command you have got in the output while setting up the master node.



Now execute the **kubectl command from the master node** to check if the node is added to the master.

```
kubectl get nodes
```

Replace `worker-node01` with the hostname of the worker node you want to label.

```
kubectl label node worker-node01  node-role.kubernetes.io/worker=worker
```

## Deploy A Sample Nginx Application

Create an Nginx deployment. Execute the following directly on the command line. It deploys the pod in the default namespace.

```
cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80      
EOF
```

Expose the Nginx deployment on a **NodePort 32000**

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector: 
    app: nginx
  type: NodePort  
  ports:
    - port: 80
      targetPort: 80
      nodePort: 32000
EOF
```

Once the deployment is up, you should be able to access the Nginx home page on the allocated NodePort:

![[Pasted image 20230915110324.png]]