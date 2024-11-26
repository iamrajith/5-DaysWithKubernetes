### **Day 4: Lab Exercise- Block, Rescue, and Always Constructs for Error Handling**

#### **Objective:**  
Participants will learn to use **block**, **rescue**, and **always** constructs in Ansible playbooks to handle errors effectively, perform recovery actions, and ensure certain tasks always execute, regardless of errors.

---

### **Lab 2.1: Basic Use of Block and Rescue**

#### **Scenario:**  
Try to create a directory under `/root/` (restricted path). If it fails, log an error message and create the directory under `/tmp/` instead.

---

#### **Steps:**

1. **Create the Playbook:**

   - Save as `~/day4/block_rescue_basic.yaml`:  
     ```yaml
     - name: Block and Rescue Demo - Basic
       hosts: localhost
       become: yes
       tasks:
         - name: Demonstrate block and rescue
           block:
             - name: Attempt to create directory in /root/
               file:
                 path: /root/sample_directory
                 state: directory
           rescue:
             - name: Log the error message
               debug:
                 msg: "Failed to create directory in /root/, attempting in /tmp/."

             - name: Create directory in /tmp/ instead
               file:
                 path: /tmp/sample_directory
                 state: directory
     ```

2. **Run the Playbook:**  

   ```bash
   ansible-playbook ~/day4/block_rescue_basic.yaml
   ```

3. **Expected Output:**  

   - The playbook fails to create the directory in `/root/` and rescues the failure by creating it in `/tmp/`.  

---

### **Lab 2.2: Using Always for Cleanup Actions**

#### **Scenario:**  
Create a temporary file during execution. Clean it up regardless of whether the tasks succeed or fail.

---

#### **Steps:**

1. **Create the Playbook:**

   - Save as `~/day4/block_always.yaml`:  
     ```yaml
     - name: Block and Always Demo
       hosts: localhost
       become: yes
       tasks:
         - name: Demonstrate block and always
           block:
             - name: Create a temporary file
               file:
                 path: /tmp/temp_file.txt
                 state: touch

             - name: Force an error
               command: /bin/false
           always:
             - name: Clean up temporary file
               file:
                 path: /tmp/temp_file.txt
                 state: absent
     ```

2. **Run the Playbook:**  

   ```bash
   ansible-playbook ~/day4/block_always.yaml
   ```

3. **Expected Output:**  

   - The playbook fails at the forced error step, but the cleanup task ensures the temporary file is deleted.  

---
### **Alternative Example for Lab 2.3: Comprehensive Example with Block, Rescue, and Always**

#### **Scenario:**  
Configure an Apache web server. If the Apache package installation fails, fall back to logging the issue and installing an alternative package (e.g., Nginx). Always ensure that any temporary files created during the process are cleaned up.

---

#### **Steps:**

1. **Create the Playbook:**

   - Save as `~/day4/block_rescue_always_alternative.yaml`:  
     ```yaml
     - name: Comprehensive Example - Apache Setup with Error Handling
       hosts: localhost
       become: yes
       tasks:
         - name: Web Server Setup with Error Handling
           block:
             - name: Create a temporary marker file
               file:
                 path: /tmp/setup_marker.txt
                 state: touch

             - name: Install Apache
               yum:
                 name: httpd
                 state: present

             - name: Start and enable Apache
               service:
                 name: httpd
                 state: started
                 enabled: true

           rescue:
             - name: Log Apache installation failure
               debug:
                 msg: "Apache installation failed. Attempting to install Nginx."

             - name: Install Nginx instead
               yum:
                 name: nginx
                 state: present

             - name: Start and enable Nginx
               service:
                 name: nginx
                 state: started
                 enabled: true

           always:
             - name: Clean up temporary files
               file:
                 path: /tmp/setup_marker.txt
                 state: absent

             - name: Log the completion of the playbook
               debug:
                 msg: "Web server setup process completed, regardless of the outcome."
     ```

---

2. **Run the Playbook:**

   ```bash
   ansible-playbook ~/day4/block_rescue_always_alternative.yaml
   ```

---

3. **Expected Output:**

   - **Successful Apache Installation:**  
     The playbook installs Apache, starts it, and cleans up the temporary file.  

   - **Apache Installation Fails:**  
     The playbook logs the error, installs Nginx as an alternative, starts Nginx, and cleans up the temporary file.

   - The `always` block ensures cleanup and logs the playbook completion message.

---

#### **Key Learning Points:**

1. **Block and Rescue:**  
   - Handle service setup failures gracefully and switch to an alternative configuration.

2. **Always:**  
   - Ensure cleanup and logging, independent of the success or failure of the block tasks.

3. **Real-World Application:**  
   - This example mirrors a common scenario in real-world deployments, teaching participants how to handle package or service installation failures dynamically.  

---

### **Optional Challenge:**
- Modify the playbook to test whether Apache or Nginx is running after the playbook execution and print a message indicating which web server is active.  
- Add additional error handling for Nginx installation failure, ensuring that a detailed log is created in `/var/log/web_server_setup.log`.


------------------------
### **Lab 2.3: Comprehensive Example with Block, Rescue, and Always**

#### **Scenario:**  
Set up a file server, ensuring key steps have error handling and cleanup.  

---

#### **Steps:**

1. **Create the Playbook:**

   - Save as `~/day4/block_rescue_always_comprehensive.yaml`:  
     ```yaml
     - name: Comprehensive Error Handling Example
       hosts: localhost
       become: yes
       tasks:
         - name: File Server Setup with Error Handling
           block:
             - name: Install NFS server package
               yum:
                 name: nfs-utils
                 state: present

             - name: Create export directory
               file:
                 path: /exports/shared
                 state: directory

             - name: Add an export to /etc/exports
               lineinfile:
                 path: /etc/exports
                 line: "/exports/shared *(rw,sync,no_root_squash)"
                 state: present

             - name: Start and enable NFS server
               service:
                 name: nfs-server
                 state: started
                 enabled: true

           rescue:
             - name: Log error and rollback
               debug:
                 msg: "Error occurred during setup. Rolling back changes."

             - name: Remove export directory
               file:
                 path: /exports/shared
                 state: absent

             - name: Remove export from /etc/exports
               lineinfile:
                 path: /etc/exports
                 line: "/exports/shared *(rw,sync,no_root_squash)"
                 state: absent

           always:
             - name: Ensure NFS service is stopped
               service:
                 name: nfs-server
                 state: stopped
     ```

2. **Run the Playbook:**  

   ```bash
   ansible-playbook ~/day4/block_rescue_always_comprehensive.yaml
   ```

3. **Expected Output:**  

   - If any step fails, the rollback tasks remove the export directory and configuration.  
   - The `always` block ensures the NFS service is stopped.  

---

### **Optional Lab: Nested Block and Rescue**

#### **Scenario:**  
Create a user and set permissions on their home directory. If user creation fails, create a default user instead. Always log actions regardless of success or failure.

---

1. **Create the Playbook:**

   - Save as `~/day4/nested_block_rescue.yaml`:  
     ```yaml
     - name: Nested Block and Rescue Demo
       hosts: localhost
       become: yes
       tasks:
         - name: Create User with Nested Error Handling
           block:
             - name: Create primary user
               user:
                 name: primary_user
                 state: present

             - name: Set permissions on home directory
               file:
                 path: /home/primary_user
                 state: directory
                 mode: '0755'
           rescue:
             - name: Log error and create default user
               debug:
                 msg: "Primary user creation failed, creating default_user."

             - name: Create default user
               user:
                 name: default_user
                 state: present
           always:
             - name: Log completion
               debug:
                 msg: "User creation process completed."
     ```

2. **Run the Playbook:**  

   ```bash
   ansible-playbook ~/day4/nested_block_rescue.yaml
   ```

3. **Expected Output:**  

   - If primary user creation fails, the playbook rescues by creating a default user.  
   - Logs actions regardless of success or failure.  

---

### **Key Learning Points:**

1. **Block and Rescue:**  
   - Handle expected errors and recover gracefully.  

2. **Always:**  
   - Ensure cleanup or important tasks are executed regardless of playbook success.  

3. **Comprehensive Error Handling:**  
   - Combine constructs to create robust, fault-tolerant playbooks.

4. **Optional Exercises:**  
   - Provide additional learning opportunities for participants to explore nested error handling scenarios.  

This lab enables participants to effectively manage errors in their Ansible playbooks with real-world applications.