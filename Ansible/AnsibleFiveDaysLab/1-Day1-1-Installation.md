### **Lab Exercise: Install Ansible and Explore Its Directory Structure**  

This lab exercise will guide you through installing Ansible on a control node, understanding its default directory structure, and verifying its setup. The lab aims to provide hands-on experience with the basics of Ansible.  

---

### **Step 1: Set Up a Control Node and Managed Nodes**

1. **Prepare a Virtual Machine (VM), Container, or Physical System as Control Node:**  
   - Use an Ubuntu/Debian-based or CentOS/RHEL-based system.  
   - Ensure the system has at least 1 GB of RAM and 10 GB of storage.  

2. **Prepare 2-3 Managed Nodes:**  
   - Use additional VMs or containers (e.g., Docker containers).  
   - Ensure SSH is enabled on managed nodes for remote communication.  

---

### **Step 2: Install Ansible on the Control Node**

#### On Ubuntu/Debian Systems:  
Run the following commands:  
```bash
sudo apt update
sudo apt install ansible -y
```

#### On CentOS/RHEL Systems:  
Enable the EPEL repository and install Ansible:  
```bash
sudo yum install epel-release -y
sudo yum install ansible -y
```

---

### **Step 3: Validate the Installation**

1. **Verify Ansible Installation:**  
   ```bash
   ansible --version
   ```  
   - Check the output for installed version, Python version, and configuration file path.

2. **Check Installed Modules (Optional):**  
   ```bash
   ansible-doc -l | head
   ```  
   This lists the modules available for use with Ansible.  

---

### **Step 4: Explore Ansible’s Default Directory Structure**

1. **Locate the Default Configuration File:**  
   The default configuration file is located at `/etc/ansible/ansible.cfg`.  
   ```bash
   ls -l /etc/ansible/ansible.cfg
   ```

2. **Inspect the Default Configuration File:**  
   View the contents of the `ansible.cfg` file:  
   ```bash
   cat /etc/ansible/ansible.cfg
   ```  
   Note the default settings for:  
   - Inventory file path  
   - Remote user  
   - SSH configuration  
   - Module options  

3. **Examine the Default Inventory File:**  
   The default inventory file is located at `/etc/ansible/hosts`.  
   ```bash
   cat /etc/ansible/hosts
   ```  
   - This file is used to define the managed nodes (hosts).  

4. **Explore the `/usr/share/ansible` Directory:**  
   This directory contains Ansible-provided roles, plugins, and modules.  
   ```bash
   ls /usr/share/ansible
   ```  
   - Investigate subdirectories for collections and roles.  

5. **Check Logs Directory (Optional):**  
   By default, logs might not be enabled. Modify the configuration to log actions:  
   - Edit the `ansible.cfg` file:  
     ```bash
     sudo vi /etc/ansible/ansible.cfg
     ```  
   - Uncomment and set `log_path` to a file (e.g., `/var/log/ansible.log`):  
     ```ini
     log_path = /var/log/ansible.log
     ```  
   - Verify logging after running any command.

---

### **Step 5: Test Ansible Setup with Ad-Hoc Commands**

1. **Ping All Managed Nodes:**  
   Add managed nodes to the default inventory (`/etc/ansible/hosts`):  
   ```ini
   [test_servers]
   node1 ansible_host=<IP_ADDRESS_1> ansible_user=<USERNAME>
   node2 ansible_host=<IP_ADDRESS_2> ansible_user=<USERNAME>
   ```  
   - Run the `ping` module to verify connectivity:  
     ```bash
     ansible test_servers -m ping
     ```

2. **Run a Simple Command on Managed Nodes:**  
   Example: Check disk space on all managed nodes:  
   ```bash
   ansible test_servers -m shell -a "uptime"
   ```
---

### **Interactive Examples for Exploration**

#### Example 1: Modify `ansible.cfg` and Test its Impact  
- Change the `timeout` value in `ansible.cfg` to 5 seconds.  
- Run a long-running command like:  
  ```bash
  ansible test_servers -m shell -a "sleep 10"
  ```  
- Observe the impact of the timeout setting by chaning the timeout value and increasing the sleep time.  



---

### **Expected Outcomes**
- Ansible is installed and functional.  
- Directory structure and configuration files are understood.  
- Basic commands are successfully executed on managed nodes.  

This interactive lab ensures participants not only set up Ansible but also gain confidence exploring its environment.