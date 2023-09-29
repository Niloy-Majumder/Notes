
## On All Nodes:

### Execute the following commands on **all the nodes** for IPtables to see bridged traffic.

Execute the below mentioned instructions:

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
```

#### Disable swap and firewall on all the Nodes

```
sudo swapoff -a
sudo ufw disable
```

#### cgroup drivers

On Linux, [control groups](https://kubernetes.io/docs/reference/glossary/?all=true#term-cgroup) are used to constrain resources that are allocated to processes.

Both the [kubelet](https://kubernetes.io/docs/reference/generated/kubelet) and the underlying container runtime need to interface with control groups to enforce [resource management for pods and containers](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) and set resources such as cpu/memory requests and limits. To interface with control groups, the kubelet and the container runtime need to use a _cgroup driver_. It's critical that the kubelet and the container runtime use the same cgroup driver and are configured the same.

There are two cgroup drivers available:

- [`cgroupfs`](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#cgroupfs-cgroup-driver)
- [`systemd`](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#systemd-cgroup-driver)

The `cgroupfs` driver is **not** recommended when [systemd](https://www.freedesktop.org/wiki/Software/systemd/) is the init system because systemd expects a single cgroup manager on the system. Additionally, if you use [cgroup v2](https://kubernetes.io/docs/concepts/architecture/cgroups), use the `systemd` cgroup driver instead of `cgroupfs`.

### Containerd Installation and Configuration

On Ubuntu:

```bash
sudo apt update
sudo apt install containerd -y
```

Containerd uses a configuration file located in `/etc/containerd/config.toml` for specifying daemon level options.The default configuration can be generated via 
`containerd config default > /etc/containerd/config.toml`. (make the directories as needed)

#### Configuring the `systemd` cgroup driver

To use the `systemd` cgroup driver in `/etc/containerd/config.toml` with `runc`, set

```toml
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  ...
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true
```

If you apply this change, make sure to restart containerd:

```shell
sudo systemctl restart containerd
```


### Install Kubeadm & Kubelet & Kubectl on all Nodes

1. Update the `apt` package index and install packages needed to use the Kubernetes `apt` repository:

```shell
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```
  
2. Download the Google Cloud public signing key:

```shell
curl -fsSL https://dl.k8s.io/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
```

3. Add the Kubernetes `apt` repository:

    ```shell
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
    ```

4. Update `apt` package index, install kubelet, kubeadm and kubectl, and pin their version:

```shell
sudo apt-get update
sudo apt-get install -y kubelet=1.27.3-00 kubectl=1.27.3-00 kubeadm=1.27.3-00
sudo apt-mark hold kubelet kubeadm kubectl
```

Add the node IP to `KUBELET_EXTRA_ARGS`.

Set `local_ip` to your machine ip: 
```bash 
local_ip=10.10.10.10
```

```bash
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
sudo kubeadm init --apiserver-advertise-address=$IPADDR --apiserver-cert-extra-sans=$IPADDR --pod-network-cidr=$POD_CIDR --node-name $NODENAME --ignore-preflight-errors Swap
```

Copy the commands to join other master nodes and worker nodes.

Verify the kubeconfig by executing the following kubectl command tolist all the pods in the `kube-system` namespace.

```
kubectl get po -n kube-system
```

*Wait for some time as coredns image creation takes time*

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