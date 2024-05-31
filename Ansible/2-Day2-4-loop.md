## Loops in Ansible Playbook

To demonstrate the 'loop' in Ansible, let's take tasks from a I playbook used in another training to configure a Kubernetes cluster.

```yaml
---
- name : Install the required packages, set the Selinux to permissive , disable the swap etc 
  hosts: all
  become : True
  tasks :
  
  - name: Ensure SELinux is set to permissive mode in the configuration file 
    lineinfile:
      path: /etc/selinux/config
      regexp: '^SELINUX='
      line: SELINUX=permissive
  
  - name: Put SELinux in permissive mode.
    ansible.posix.selinux:
      policy: targeted
      state: permissive

  - name: Configure required modules
    shell : modprobe overlay ; modprobe br_netfilter
```

Let's break down the playbook and then enhance the last task to utilize an Ansible loop.

1. **Setting SELinux to Permissive Mode**:
   - The first two tasks focus on SELinux configuration.
   - In the `lineinfile` task, we ensure that the `SELINUX` line in the `/etc/selinux/config` file is set to `permissive`.
   - The `ansible.posix.selinux` task puts SELinux in permissive mode for the targeted policy.

2. **Configuring Required Modules**:
   - The third task executes a shell command to load two kernel modules: `overlay` and `br_netfilter`.
   - These modules are essential for containerization and network filtering.

3. **Enhancing the Last Task with an Ansible Loop**:
   - Instead of using a shell command directly, let's modify the last task to use an Ansible loop.
   - We'll use the `loop` keyword to iterate over a list of module names and load them.
   - Here's the updated task:

```yaml
  - name: Configure required modules using Ansible loop
    shell: "modprobe {{ item }}"
    loop:
      - overlay
      - br_netfilter
```

   - In this new task:
     - The `shell` module runs the `modprobe` command for each item in the loop.
     - The `item` variable represents the current module name (e.g., `overlay`, `br_netfilter`).

Now your playbook will dynamically load the specified modules using the Ansible loop!

One other section of the playbook that can be optimized with 'loop' is given below.

```yaml

  - name: Configure sysctl parameter 
    lineinfile:
      create: yes
      path: /etc/sysctl.d/99-kubernetes-cri.conf
      line: net.bridge.bridge-nf-call-iptables  = 1

  - name: Configure sysctl parameter 
    lineinfile:
      path: /etc/sysctl.d/99-kubernetes-cri.conf
      line: net.ipv4.ip_forward                 = 1

  - name: Configure sysctl parameter 
    lineinfile:
      path: /etc/sysctl.d/99-kubernetes-cri.conf
      line: net.bridge.bridge-nf-call-ip6tables = 1

  - name: Update the change in the configuration 
    shell: sysctl --system

```

We'll consolidate this into a single task that iterates over the required parameters.

```yaml
- name: Configure sysctl parameters using Ansible loop
  lineinfile:
    create: yes
    path: /etc/sysctl.d/99-kubernetes-cri.conf
    line: "{{ item }}"
  loop:
    - "net.bridge.bridge-nf-call-iptables = 1"
    - "net.ipv4.ip_forward = 1"
    - "net.bridge.bridge-nf-call-ip6tables = 1"

- name: Update the changes in the configuration
  shell: sysctl --system
```

In this updated task:
- The `lineinfile` module will add each parameter line to the specified file.
- The `loop` iterates over the list of parameters, ensuring cleaner and more concise code.

Here is another instance of 'loop' in the same playbook, which should be self-explanatory. Like this, the playbooks can be simplified and enhanced with appropriate loops.

```yaml

  - name: Add kubernetes repository
    yum_repository:
      name: Kubernetes
      description: Kubernetes
      baseurl: 'https://pkgs.k8s.io/core:/stable:/v1.28/rpm/'
      #baseurl: https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
      enabled: True
      gpgcheck: False
      gpgcakey : 'https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key'
      #gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
      #exclude: kubelet kubeadm kubectl cri-tools kubernetes-cni

# Kubelet will not start if the system has swap enabled, so let us disable the swap.
  - name: Remove swapfile from /etc/fstab
    mount:
      name: "{{ item }}"
      fstype: swap
      state: absent
    with_items:
      - swap
      - none

  - name: Disable swap
    command: swapoff -a
    when: ansible_swaptotal_mb > 0  

  - name: Install a list of packages for kubernetes
    yum:
      name:
        - kubelet
        - kubeadm
        - kubectl
        - containerd
         
      state: present
```

### Reference 
[Loops — Ansible Community Documentation.](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_loops.html.)