
### **Lab Exercise: for Debugging Techniques and Troubleshooting**  

**Objective:**  
The goal of this exercise is to help participants understand and practice debugging techniques in Ansible using **syntax check**, **check mode**, **diff mode**, and **verbose logging**. Participants will be provided with intentionally incorrect playbooks to identify and correct errors, as well as examples to understand how each debugging technique works.

---

### **Exercise 1: Syntax Errors**  
**Objective:**  
Identify and fix syntax errors in the provided playbooks.

#### **Instructions:**  
The following playbooks contain intentional syntax errors. Participants should identify and correct them. Run the playbooks after correcting the errors.

#### **Playbook 1:**
```yaml
- name: Install NGINX on Amazon Linux
  hosts: all
  tasks:
    - name: Install nginx
      yum
        name: nginx
        state: present
    - name: Start nginx service
      services:
        name: nginx
        state: started
```
**Issues to correct:**
- Look for any indentation or syntax errors in the playbook that would prevent it from running.

#### **Playbook 2:**
```yaml
- name: Set up firewall rules
  hosts: all
  tasks:
    - name: Allow HTTP traffic
      firewalld:
        service: http
        permanent: true
        state: enabled
    - name: Reload firewall rules
      firewalld:
        state: reloaded
        immediate: yes
```
**Issues to correct:**
- Look for missing or incorrect module parameters that may cause syntax issues.

#### **Playbook 3:**
```yaml
- name: Install software and configure a directory
  hosts: all
  tasks:
    - name: Install git
      yum:
        name: git
        state: present

    - name: Create a directory for repositories
        file:
        path: /opt/repositories
        state: directory
        mode: 0755
        owner: root
        group: root

    - name: Clone a repository
      git:
          repo: https://github.com/example/repo.git
        dest: /opt/repositories/myrepo
        version: master
```
**Issues to correct:**
- Identify and fix the issues.

#### **Objective for Participants:**  
- Use `ansible-playbook --syntax-check` to check for syntax errors.
- Correct the identified errors and re-test the playbook.

---

### **Exercise 2: Check Mode**  
**Objective:**  
Understand the usage of **Check Mode** by simulating the execution of a playbook without making any actual changes.

#### **Instructions:**  
The following playbooks will help participants understand how to use check mode. Participants should run these playbooks with the `--check` option to see the planned changes without actually applying them.

#### **Playbook 1:**
```yaml
- name: Install and Start Apache Server
  hosts: all
  tasks:
    - name: Install Apache
      yum:
        name: httpd
        state: present
    - name: Start Apache service
      service:
        name: httpd
        state: started
```
**Run with:**
```bash
ansible-playbook playbook.yml --check
```
**Expected Outcome:**  
- The output will show which tasks will be executed but without actually installing or starting Apache.

#### **Playbook 2:**
```yaml
- name: Create Directory and Copy File
  hosts: all
  tasks:
    - name: Create a directory
      file:
        path: /tmp/example_dir
        state: directory
    - name: Copy a file
      copy:
        src: /path/to/file.txt
        dest: /tmp/example_dir/file.txt
```
**Run with:**
```bash
ansible-playbook playbook.yml --check
```
**Expected Outcome:**  
- The output will display the tasks that would be performed (creating the directory and copying the file) but won’t actually modify the system.

#### **Objective for Participants:**  
- Use the `--check` option to simulate running a playbook without applying any changes.
- Understand the impact of tasks before executing them.

---

### **Exercise 3: Diff Mode**  
**Objective:**  
Understand how to view file changes using **diff mode** in Ansible.

#### **Instructions:**  
Participants will modify a playbook to include file changes and will observe the differences when running the playbook with `--diff`.

#### **Playbook 1:**
```yaml
- name: Configure nginx index.html
  hosts: all
  tasks:
    - name: Add content to the index.html file
      lineinfile:
        path: /var/www/html/index.html
        line: "Welcome to the Apache Web Server!"
    - name: Add another line to index.html
      lineinfile:
        path: /var/www/html/index.html
        line: "This is the second line added."
```
**Run with:**
```bash
ansible-playbook playbook.yml --diff
```
**Expected Outcome:**  
- The output will display the changes made to the `index.html` file, showing the differences (`+` or `-` symbols) in the file contents.

#### **Playbook 2:**
```yaml
- name: Modify configuration file
  hosts: all
  tasks:
    - name: Modify nginx.conf file
      lineinfile:
        path: /etc/nginx/nginx.conf
        regexp: '^#user'
        line: 'user nginx;'
```
**Run with:**
```bash
ansible-playbook playbook.yml --diff
```
**Expected Outcome:**  
- The output will show the change made to the `nginx.conf` file with diff highlighting, where it will show which lines were modified.

#### **Objective for Participants:**  
- Use the `--diff` option to visualize changes in files managed by Ansible.
- Understand how to identify file modifications using the diff output.

---

### **Exercise 4: Verbose Logging**  
**Objective:**  
Understand how to use **verbose logging** to troubleshoot issues and get detailed information on playbook execution.

#### **Instructions:**  
Participants will run a playbook with verbose logging enabled using the `-vvv` option.

#### **Playbook 1:**
```yaml
- name: Install and configure nginx
  hosts: all
  tasks:
    - name: Install nginx
      yum:
        name: nginx
        state: present
    - name: Start nginx service
      service:
        name: nginx
        state: started
    - name: Create index.html
      copy:
        content: "Welcome to Nginx!"
        dest: /usr/share/nginx/html/index.html
```
**Run with:**
```bash
ansible-playbook playbook.yml -vvv
```
**Expected Outcome:**  
- The verbose output will show detailed logs of each task's execution, including task start, success/failure messages, and more detailed information about each action.

#### **Playbook 2:**
```yaml
- name: Add a new user and group
  hosts: all
  tasks:
    - name: Create a new user
      user:
        name: exampleuser
        state: present
    - name: Add user to a group
      user:
        name: exampleuser
        groups: sudo
        append: true
```
**Run with:**
```bash
ansible-playbook playbook.yml -vvv
```
**Expected Outcome:**  
- The detailed verbose output will show each task’s execution details, including variable values and task results.

#### **Objective for Participants:**  
- Use the `-vvv` option to enable verbose logging.
- Understand how verbose output helps troubleshoot issues by providing more information about task execution.

---

### **Conclusion:**  
By completing these exercises, participants will gain hands-on experience with **syntax checks**, **check mode**, **diff mode**, and **verbose logging** in Ansible. These techniques will help them troubleshoot playbooks efficiently and understand the impact of their changes before executing them on real systems.