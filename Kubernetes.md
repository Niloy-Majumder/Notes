Kubernetes, also known as K8s, is an open-source system for automating deployment, scaling, and management of containerized applications. It is a Container orchestration tool.


To create a cluster:

On master node : 
```
kubeadm init
```

To reset made by kubeadm init and kubeadm join:

```
sudo kubeadm reset && sudo rm -r /etc/cni/net.d/ && rm -r \$HOME/.kube/
```


list all the pods in the `kube-system` namespace.

```
kubectl get po -n kube-system
```

verify all the cluster component health statuses 

```
kubectl get --raw='/readyz?verbose'
```

Get the cluster info 

```
kubectl cluster-info 
```

If you missed copying the join command, execute the following command in the master node to recreate the token with the join command.

```
kubeadm token create --print-join-command
```

Get All Components 

```
kubectl get all
```

Get extra details

```
kubectl get all -o wide
```

Get pod/deployment/service

```
kubectl get [component]
```

Edit ConfigMap for kube-proxy:

```
kubectl edit cm/kube-proxy -n kube-system
```

Rollout the changes:

```
kubectl rollout status ds kube-proxy -n kube-system
```