### **Lab Exercise: Test Ansible Setup with Ad-Hoc Commands**  

In this step, participants will execute various ad-hoc commands to validate their Ansible setup and perform common tasks. These exercises are divided into **core exercises** and **optional exercises** for those who complete the basics early can go for the **optional exercises**.

---

### **Core Exercises (Mandatory for All Participants)**  

#### **Exercise 1: Ping Managed Nodes**  
- Use the `ping` module to verify connectivity to all managed nodes.  
  ```bash
  ansible all -m ping
  ```  
  - **Expected Output:**  
    A green success message confirming that the managed nodes are reachable.  

---

#### **Exercise 2: Check Host Uptime**  
- Use the `command` module to execute the `uptime` command on all nodes.  
  ```bash
  ansible all -m command -a "uptime"
  ```  
  - **Expected Output:**  
    Uptime details of each managed node.

---

#### **Exercise 3: Create a User on Managed Nodes**  
- Use the `user` module to create a new user named `ansible_userXX`.  
  ```bash
  ansible all -m user -a "name=ansible_userXX state=present"
  ```  
  - **Expected Output:**  
    A new user `ansible_userXX` is created on all managed nodes.  

---

#### **Exercise 4: Verify the User avilability on Nodes**  
- Use the `user` module to create a new user named `ansible_userXX`.  
  ```bash
  ansible all -m shell -a "id sible_userXX"
  ```  
  - **Expected Output:**  
    The new user `ansible_userXX` should be present on all managed nodes.  

---
#### **Exercise 5: Install a Package (e.g., `tree`)**  
- Use the `yum` or `apt` module to install the `tree` package.  
  ```bash
  ansible all -m yum -a "name=tree state=present"  # CentOS/RHEL
  ansible all -m apt -a "name=tree state=present"  # Ubuntu/Debian
  ```  
If it is already installed, please use "state=absent" then execute the above command angain observe the diffrence.
 ```bash
  ansible all -m yum -a "name=tree state=absent"  # CentOS/RHEL
  ansible all -m apt -a "name=tree state=absent"  # Ubuntu/Debian
  ```  

  - **Expected Output:**  
    The `tree` package is installed on all managed nodes.  

---

#### **Exercise 6: Start a Service**  
- Use the `service` module to start the `httpd` service (or install it first if not present).  
  ```bash
  ansible all -m yum -a "name=httpd state=present"  # Install httpd
  ansible all -m service -a "name=httpd state=started enabled=yes"
  ```  
  - **Expected Output:**  
    The `httpd` service is installed, started, and enabled on all managed nodes.

---

### **Optional Exercises **  

#### **Exercise 7: Gather System Facts**  
- Use the `setup` module to gather facts about managed nodes and display the hostname.  
  ```bash
  ansible all -m setup -a "filter=ansible_hostname"
  ```  
  - **Expected Output:**  
    The hostname of each managed node.  

---

#### **Exercise 8: Copy Files to Managed Nodes**  
- Use the `copy` module to copy a file from the control node to `/tmp/` on managed nodes.  
  - Create a file on the control node:  
    ```bash
    echo "Welcome to Ansible Training" > /tmp/welcome.txt
    ```  
  - Copy the file:  
    ```bash
    ansible all -m copy -a "src=/tmp/welcome.txt dest=/tmp/"
    ```  
  - Verify on managed nodes:  
    ```bash
    cat /tmp/welcome.txt
    ```  

---

#### **Exercise 9: Execute a Shell Script on Managed Nodes**  
- Create a simple shell script on the control node:  
  ```bash
  echo -e "#!/bin/bash\nhostname" > /tmp/hostname.sh
  chmod +x /tmp/hostname.sh
  ```  
- Use the `script` module to execute the script on all nodes:  
  ```bash
  ansible all -m script -a "/tmp/hostname.sh"
  ```  
  - **Expected Output:**  
    The hostname of each managed node is displayed.  

---

#### **Exercise 10: Manage Files on Managed Nodes**  
- Use the `file` module to create a directory `/tmp/ansible_test`:  
  ```bash
  ansible all -m file -a "path=/tmp/ansible_test state=directory mode=0755"
  ```  
- Use the `file` module to delete the directory:  
  ```bash
  ansible all -m file -a "path=/tmp/ansible_test state=absent"
  ```  

---

#### **Exercise 11: Modify File Contents**  
- Use the `lineinfile` module to add a line to `/etc/motd` on managed nodes:  
  ```bash
  ansible all -m lineinfile -a "path=/etc/motd line='Managed by Ansible' create=yes"
  ```  
  - **Expected Output:**  
    The line "Managed by Ansible" is added to the `/etc/motd` file.  

---

#### **Exercise 12: Retrieve Files from Managed Nodes**  
- Use the `fetch` module to retrieve a file (e.g., `/var/log/messages`) from managed nodes to the control node.  
  ```bash
  ansible all -m fetch -a "src=/var/log/messages dest=/tmp/ logs/ flat=yes"
  ```  
  - **Expected Output:**  
    Files are copied to the specified location on the control node.  

---

### **Summary of Commands**

| **Exercise**         | **Command/Module**              | **Purpose**                                   |  
|-----------------------|---------------------------------|-----------------------------------------------|  
| Ping                 | `ping`                         | Test connectivity to managed nodes           |  
| Uptime               | `command`                      | Execute shell commands                       |  
| Create User          | `user`                         | Manage system users                          |  
| Install Package      | `yum/apt`                      | Install packages                             |  
| Start Service        | `service`                      | Manage system services                       |  
| Gather Facts         | `setup`                        | Collect system information                   |  
| Copy Files           | `copy`                         | Copy files to managed nodes                  |  
| Run Scripts          | `script`                       | Execute scripts on managed nodes             |  
| Manage Directories   | `file`                         | Create or delete directories                 |  
| Modify Files         | `lineinfile`                   | Modify file contents                         |  
| Retrieve Files       | `fetch`                        | Copy files from managed nodes to control node|  

---

### **Outcome of This**  
- Participants will understand how to use Ansible ad-hoc commands effectively.  
- They will gain confidence performing tasks like file management, service handling, and system information gathering.  
- Fast learners can attempt optional exercises to deepen their knowledge and explore advanced modules.