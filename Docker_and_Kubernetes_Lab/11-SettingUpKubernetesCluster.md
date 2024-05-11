# Setting Up a Kubernetes Cluster: Post-Installation Steps

## Congratulations! Your Kubernetes cluster is ready, and we’re setting up the user environment.

We are only a few steps away from the finish line. Soon, you will be able to create pods, deployments, and more in your cluster.

### 1. Configure `kubectl` for Regular Users

To use `kubectl` (the Kubernetes command-line tool), follow these steps:

1. Create the `.kube` directory in your home folder:

*(Note: Execute the below command from the user where you want to manage your Kubernetes cluster.)*
```bash
mkdir -p $HOME/.kube
```

2. Copy the Kubernetes configuration file to your user's home directory:
```bash
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```

3. Change ownership of the configuration file to your user:
```bash
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
### 2. Verify Node Status

Your node status is not ready yet! Don't worry, it will be ready soon.
```bash
kubectl get nodes
```

### 3. Set Up Autocomplete for `kubectl`

To enable autocomplete for `kubectl` in your current shell session, run:
```bash
source <(kubectl completion bash)
```

To make this change permanent, add the following line to your `~/.bashrc` file:
```bash
echo "source <(kubectl completion bash)" >> ~/.bashrc
```

### 4. Install WeaveNet CNI

WeaveNet is a popular Container Network Interface (CNI) plugin for Kubernetes. It provides networking between pods across different nodes in the cluster.

Execute the following command to apply the WeaveNet CNI configuration:
```bash
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```

### 5. Verify Node Status

After applying WeaveNet, check the status of your nodes:
```bash
kubectl get nodes
```
Your nodes should now show the status as "Ready."


### 2. Join Additional Nodes to the Cluster , 

**This need to be execute from the worker nodes.**

To add more nodes to your cluster, run the following command on each node as the root user:
```bash
kubeadm join <control-plane-host>:<control-plane-port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```
Replace `<control-plane-host>`, `<control-plane-port>`, `<token>`, and `<hash>` with the appropriate values.

Alternatively, you can get the same join command by executing the following on the master node:
```bash
kubeadm token create --print-join-command
```


Now you're all set! Your Kubernetes cluster is ready. 

---

Feel free to make any necessary modifications to this document. If you have any questions or additional requests, please let me know. 😊

## References
- [kubectl Quick Reference](https://kubernetes.io/docs/reference/kubectl/quick-reference/#bash)
- [Creating a cluster with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)
