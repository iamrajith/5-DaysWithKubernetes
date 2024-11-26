### **Lab Exercise: Managing Ansible Inventories Effectively (INI Format)**  

This lab exercise focuses on managing and testing Ansible inventories in **INI format**. Exercises are designed with core and optional tasks, focusing on practical commands like `uptime`, `date`, checking the OS version, release, and kernel version. The text editor is updated to use **`vi`** for file management.  

---

### **Core Exercises (Mandatory)**  

#### **Exercise 1: Create a Basic Static Inventory**  
1. **Create an Inventory File:**  
   - Create a file named `inventory.ini` using `vi`:  
     ```bash
     vi ~/inventory.ini
     ```  

   - Add the following content and save the file:  
     ```ini
     [web]
     web1 ansible_host=192.168.1.10 ansible_user=userxx ansible_ssh_pass=password
     web2 ansible_host=192.168.1.11 ansible_user=userxx ansible_ssh_pass=password

     [db]
     db1 ansible_host=192.168.1.20 ansible_user=userxx ansible_ssh_pass=password
     db2 ansible_host=192.168.1.21 ansible_user=userxx ansible_ssh_pass=password
     ```

2. **Ping All Hosts in the Inventory:**  
   Test connectivity to all hosts:  
   ```bash
   ansible all -i ~/inventory.ini -m ping
   ```  
   - **Expected Output:** Success messages confirming connectivity to all hosts.

---

#### **Exercise 2: Group Hosts and Use Patterns**  
1. **Group Inventory by Environment:**  
   - Edit `inventory.ini` using `vi`:  
     ```bash
     vi ~/inventory.ini
     ```  

   - Modify the content to include groups:  
     ```ini
     [web]
     web1 ansible_host=192.168.1.10
     web2 ansible_host=192.168.1.11

     [db]
     db1 ansible_host=192.168.1.20
     db2 ansible_host=192.168.1.21

     [prod:children]
     web
     db
     ```

2. **Run Commands Using Group Names:**  
   - Test the `prod` group:  
     ```bash
     ansible prod -i ~/inventory.ini -m command -a "uptime"
     ```  

3. **Run Commands Using Patterns:**  
   - Ping only the first host in each group:  
     ```bash
     ansible web[0] -i ~/inventory.ini -m ping
     ```  
   - Run a command on all hosts except those in the `web` group:  
     ```bash
     ansible all:!web -i ~/inventory.ini -m command -a "date"
     ```  

---

#### **Exercise 3: Check System Information**  
1. **Check OS Version and Release:**  
   - Use the `command` module to check OS version:  
     ```bash
     ansible all -i ~/inventory.ini -m command -a "cat /etc/os-release"
     ```  

2. **Check Kernel Version:**  
   - Run the following command to retrieve the kernel version:  
     ```bash
     ansible all -i ~/inventory.ini -m command -a "uname -r"
     ```  

3. **Verify Disk Space Usage:**  
   - Use the `command` module to check disk space:  
     ```bash
     ansible all -i ~/inventory.ini -m command -a "df -h"
     ```  

---

#### **Exercise 4: Use Variables in Inventory**  
1. **Add Variables to Groups:**  
   - Edit `inventory.ini` using `vi`:  
     ```bash
     vi ~/inventory.ini
     ```  

   - Add group-specific variables:  
     ```ini
     [web]
     web1 ansible_host=192.168.1.10 ansible_user=userxx ansible_ssh_pass=password
     web2 ansible_host=192.168.1.11 ansible_user=userxx ansible_ssh_pass=password

     [db]
     db1 ansible_host=192.168.1.20 ansible_user=userxx ansible_ssh_pass=password
     db2 ansible_host=192.168.1.21 ansible_user=userxx ansible_ssh_pass=password

     [web:vars]
     http_home=/var/www/html
     max_connections=100

     [db:vars]
     db_name=inventory
     db_timeout=30
     ```

2. **Verify Variables:**  
   - Use the `setup` module to check variables:  
     ```bash
     ansible web -i ~/inventory.ini -m setup -a "filter=ansible_local"
     ```  

---

### **Optional Exercises (For Fast Learners)**  

#### **Exercise 5: Add Nested Groups**  
1. **Enhance the Inventory:**  
   - Add more groups to `inventory.ini`:  
     ```ini
     [dev]
     dev1 ansible_host=192.168.2.10 ansible_user=userxx ansible_ssh_pass=password
     dev2 ansible_host=192.168.2.11 ansible_user=userxx ansible_ssh_pass=password

     [all:children]
     prod
     dev
     ```

2. **Run Commands on Nested Groups:**  
   - Test the `all` group:  
     ```bash
     ansible all -i ~/inventory.ini -m ping
     ```  

---

#### **Exercise 6: Validate Inventory Syntax**  
- Use the `ansible-inventory` command to validate the inventory file:  
  ```bash
  ansible-inventory -i ~/inventory.ini --list
  ```  
  - **Expected Output:** JSON-formatted inventory details.

---

#### **Exercise 7: Combine Patterns**  
1. **Run Commands Using Advanced Patterns:**  
   - Check kernel version for all hosts in the `prod` group except `db2`:  
     ```bash
     ansible prod:!db2 -i ~/inventory.ini -m command -a "uname -r"
     ```

2. **Limit Execution to Specific Hosts:**  
   - Check the OS release for a specific host (`web1`):  
     ```bash
     ansible web1 -i ~/inventory.ini -m command -a "cat /etc/os-release"
     ```  

---

### **Expected Outcomes**  
- Participants will be able to:  
  - Create and manage inventory files using **`vi`**.  
  - Test connectivity and execute various system commands like checking uptime, date, OS version, kernel version, and disk space.  
  - Organize hosts into groups and manage variables effectively.  
  - Use patterns and advanced inventory techniques to target specific hosts or groups.  

- **Optional exercises** enable participants to explore nested groups, validate inventory syntax, and use advanced patterns for dynamic host targeting.