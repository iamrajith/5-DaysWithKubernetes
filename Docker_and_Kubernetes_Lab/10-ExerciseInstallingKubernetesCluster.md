# Lab Exercise: Installing a Kubernetes Cluster

Follow these steps to install Kubernetes on Amazon Linux:

### Prerequisites:
- An Amazon Linux instance (e.g., EC2 instance)
- Basic knowledge of Linux commands

### Steps:

1. **Disable SELinux**:
   - Edit the SELinux configuration file:
     ```bash
     vi /etc/selinux/config
     ```
   - Change `SELINUX` to `permissive` and `policy` to `targeted`:
     ```ini
     SELINUX=permissive
     ```

2. **Verify SELinux Status**:
   - If SELinux is disabled, edit the configuration and reboot the server.
   - Otherwise, execute the command. This will change it from enforcing to permissive.
     ```bash
     setenforce permissive
     ```

3. **Load Kernel Modules**:
   - Execute the following commands:
     ```bash
     modprobe overlay
     modprobe br_netfilter
     ```

4. **Make Kernel Modules Permanent**:
   - Edit the file `/etc/modules-load.d/containerd.conf` and add the following lines:
     ```ini
     overlay
     br_netfilter
     ```

5. **Configure sysctl Settings**:
   - Create or edit the file `/etc/sysctl.d/99-kubernetes-cri.conf`:
     ```bash
    vi /etc/sysctl.d/99-kubernetes-cri.conf
     ```
   - Add the following lines:
     ```ini
     net.bridge.bridge-nf-call-iptables = 1
     net.ipv4.ip_forward = 1
     net.bridge.bridge-nf-call-ip6tables = 1
     ```
   - Apply the sysctl settings:
     ```bash
     sysctl --system
     ```

6. **Disable Swap and Remove Fstab Entry**:
   - Disable swap:
     ```bash
     swapoff -a
     ```
   - Remove the swap entry from `/etc/fstab`.

7. **Add Kubernetes Repository**:
   - Create or edit the file `/etc/yum.repos.d/kubernetes.repo`:
     ```bash
    vi /etc/yum.repos.d/kubernetes.repo
     ```
   - Add the following content:
     ```ini
     [kubernetes]
     name=Kubernetes
     baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
     enabled=1
     gpgcheck=1
     gpgkey=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key
     exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
     ```

8. **Install Kubernetes Components**:
   - Install `kubelet`, `kubeadm`, and `kubectl`:
     ```bash
     yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
     ```
   - Enable the kubelet service:
     ```bash
     systemctl enable kubelet
     systemctl start kubelet
     ```
   - Install `containerd`:
     ```bash
     yum install -y containerd 
     ```
    
9. **Create Containerd Configuration**:
   - Run the following command:
     ```bash
     containerd config default | sudo tee /etc/containerd/config.toml
     ```
   - Edit `/etc/containerd/config.toml`: search for the below string and modifiy it to true.
      ```bash
      SystemdCgroup = true
      ```
   - Start containerd service :

      ```bash
      systemctl status containerd
      systemctl start  containerd
      systemctl enable   containerd

      ```

10. **Configure Node IP**:
 
    - Edit `/etc/default/kubelet`:
      ```bash
      KUBELET_EXTRA_ARGS=--node-ip={{ node_ip }}
      ```

11. **Initialize the Kubernetes Cluster**:
    - Run the following command (replace `{IP address of the server}` with your server's IP):
      ```bash
      sudo kubeadm init --apiserver-advertise-address="{IP address of the server}" --apiserver-cert-extra-sans="{IP address of the server}" --node-name master --pod-network-cidr=192.168.0.0/24
      ```

## References
- [Kubernetes Installation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
- [Creating a cluster with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)