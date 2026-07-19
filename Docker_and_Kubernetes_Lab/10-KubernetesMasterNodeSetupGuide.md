
# 🚀 Kubernetes Master Node Setup Guide

This document provides a step-by-step walkthrough for setting up a **Kubernetes Master Node** using `kubeadm`.  
We’ll cover system preparation, container runtime setup, Kubernetes installation, cluster initialization, and networking.

---

## 📌 1. System Preparation

### Update and Upgrade Packages
```bash
apt update && sudo apt upgrade -y
```
Keeps your system up-to-date with the latest security patches and features.

---

### Install Required Dependencies
```bash
apt install -y apt-transport-https ca-certificates curl gnupg lsb-release
```
These packages ensure secure communication and repository management.

---

### Disable Swap
```bash
swapoff -a
sed -i '/ swap / s/^/#/' /etc/fstab
```
Kubernetes requires swap to be disabled for proper scheduling.

---

## 📌 2. Kernel Modules and Sysctl Configuration

### Load Required Modules
```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

modprobe overlay
modprobe br_netfilter
```

### Configure Networking Parameters
```bash
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sysctl --system
```
This ensures proper packet forwarding and network bridging for Kubernetes.

---

## 📌 3. Install and Configure Container Runtime (Containerd)

```bash
apt install -y containerd
mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
systemctl restart containerd
systemctl enable containerd
```

✅ **Note:** Kubernetes recommends using `systemd` as the cgroup driver for better compatibility.

---

## 📌 4. Install Kubernetes Components

### Add Kubernetes Repository
```bash
mkdir -p /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.35/deb/Release.key | \
sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
https://pkgs.k8s.io/core:/stable:/v1.35/deb/ /" | \
sudo tee /etc/apt/sources.list.d/kubernetes.list
```

### Install Kubernetes Binaries
```bash
apt update
apt install -y kubelet kubeadm kubectl  
apt-mark hold kubelet kubeadm kubectl
```

---

## 📌 5. Initialize Kubernetes Master Node

```bash
kubeadm init \
--apiserver-advertise-address="172.16.10.<Replace with your IP >" \
--apiserver-cert-extra-sans="172.16.10.< Replace with your IP >" \
--node-name master \
--pod-network-cidr=192.168.0.0/24
```

✅ This command bootstraps the control plane.  
The `--pod-network-cidr` must match your chosen CNI plugin (here, Calico).

---

## 📌 6. Configure kubectl for the Admin User

```bash
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
```

Now you can run `kubectl` commands as a non-root user.

---

## 📌 7. Install Pod Network (Calico)

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.32.0/manifests/calico.yaml
```

Verify:
```bash
kubectl get nodes
kubectl get pods -A
```

---

## 📌 8. Worker Node Join Command

Generate the join command:
```bash
kubeadm token create --print-join-command
```

Run this command on worker nodes to join them to the cluster.

---

## 📌 9. Test Deployment

```bash
kubectl create deployment demo1 --image nginx --replicas 10
kubectl get pods
kubectl describe pod <pod-name>
```

This validates that the cluster is functional and workloads can be scheduled.

---

# ✅ Final Verification

- Check nodes:
```bash
kubectl get nodes
```

- Check pods:
```bash
kubectl get pods -A
```

---

# 🎯 Summary

You have successfully:
1. Prepared the system  
2. Installed container runtime  
3. Installed Kubernetes components  
4. Initialized the master node  
5. Configured networking with Calico  
6. Joined worker nodes  
7. Deployed a test workload  

---