### **Day 5: Lab Series 2 - Introduction to Roles and Their Directory Structure**

#### **Objective:**  
This lab introduces participants to **Ansible roles** and their structured directory organization, enabling efficient and reusable playbook design. Participants will learn to create, organize, and use roles in playbooks.

---

### **Lab 2.1: Creating a Basic Role**

#### **Scenario:**  
Set up a web server using an Ansible role.

#### **Steps:**

1. **Create the Role Structure:**

   Use the `ansible-galaxy` command to create a role named `webserver`:
   ```bash
   ansible-galaxy init ~/day5/roles/webserver
   ```

   Directory structure will look like this:
   ```
   ~/day5/roles/webserver/
   ├── defaults/
   ├── files/
   ├── handlers/
   ├── meta/
   ├── tasks/
   ├── templates/
   ├── tests/
   └── vars/
   ```

2. **Define the Role Tasks:**

   Edit `~/day5/roles/webserver/tasks/main.yaml`:
   ```yaml
   ---
   - name: Install Apache
     yum:
       name: httpd
       state: present

   - name: Start and enable Apache
     service:
       name: httpd
       state: started
       enabled: true
   ```

3. **Define Default Variables:**

   Edit `~/day5/roles/webserver/defaults/main.yaml`:
   ```yaml
   ---
   web_port: 80
   ```

4. **Add a Handler:**

   Edit `~/day5/roles/webserver/handlers/main.yaml`:
   ```yaml
   ---
   - name: Restart Apache
     service:
       name: httpd
       state: restarted
   ```

5. **Create a Template:**

   Save as `~/day5/roles/webserver/templates/index.html.j2`:
   ```html
   <html>
   <head>
       <title>Welcome to {{ inventory_hostname }}</title>
   </head>
   <body>
       <h1>Apache is running on {{ ansible_facts['os_family'] }}!</h1>
   </body>
   </html>
   ```

6. **Modify the Role Tasks to Use the Template:**

   Update `~/day5/roles/webserver/tasks/main.yaml`:
   ```yaml
   ---
   - name: Install Apache
     yum:
       name: httpd
       state: present

   - name: Deploy web page
     template:
       src: index.html.j2
       dest: /var/www/html/index.html
       mode: '0644'

   - name: Start and enable Apache
     service:
       name: httpd
       state: started
       enabled: true
   ```

7. **Create a Playbook to Use the Role:**

   Save as `~/day5/webserver_role_playbook.yaml`:
   ```yaml
   ---
   - name: Deploy Web Server
     hosts: webservers
     roles:
       - role: webserver
   ```

8. **Run the Playbook:**

   ```bash
   ansible-playbook ~/day5/webserver_role_playbook.yaml -i inventory
   ```

---

### **Lab 2.2: Adding Role Variables**

#### **Scenario:**  
Customize the web server setup by modifying role variables.

#### **Steps:**

1. **Update the Default Variable:**  
   Edit `~/day5/roles/webserver/defaults/main.yaml` to include:
   ```yaml
   ---
   web_port: 8080
   ```

2. **Modify the Tasks to Use the Variable:**

   Update `~/day5/roles/webserver/tasks/main.yaml`:
   ```yaml
   ---
   - name: Configure Apache to Listen on Custom Port
     lineinfile:
       path: /etc/httpd/conf/httpd.conf
       regexp: '^Listen '
       line: "Listen {{ web_port }}"
       state: present
       notify: Restart Apache
   ```

3. **Run the Playbook:**

   ```bash
   ansible-playbook ~/day5/webserver_role_playbook.yaml -e web_port=8080 -i inventory
   ```

4. **Exercise:**  
   - Modify the role to support SSL configuration by adding another task and variable for the SSL module.

---

### **Lab 2.3: Using Multiple Roles in a Single Playbook**

#### **Scenario:**  
Set up both a web server and a database server using separate roles.

#### **Steps:**

1. **Create a Role for the Database Server:**

   Use `ansible-galaxy init`:
   ```bash
   ansible-galaxy init ~/day5/roles/dbserver
   ```

2. **Define Tasks for the Database Server Role:**

   Edit `~/day5/roles/dbserver/tasks/main.yaml`:
   ```yaml
   ---
   - name: Install MariaDB
     yum:
       name: mariadb-server
       state: present

   - name: Start and enable MariaDB
     service:
       name: mariadb
       state: started
       enabled: true
   ```

3. **Modify the Master Playbook:**

   Save as `~/day5/multi_role_playbook.yaml`:
   ```yaml
   ---
   - name: Deploy Web Server
     hosts: webservers
     roles:
       - role: webserver

   - name: Deploy Database Server
     hosts: dbservers
     roles:
       - role: dbserver
   ```

4. **Run the Master Playbook:**

   ```bash
   ansible-playbook ~/day5/multi_role_playbook.yaml -i inventory
   ```

---

### **Optional Lab: Debugging and Troubleshooting Roles**

#### **Scenario:**  
Test failure scenarios in roles (e.g., missing variables, incorrect templates). Debug and fix the issues.

1. Remove a required file or variable in the role.
2. Run the playbook and observe the error.
3. Add error handling or default values to fix the issue.

---

### **Key Learning Points:**

1. **Ansible Role Structure:**  
   Understand the modular and organized directory structure of roles.

2. **Reusability and Scalability:**  
   Roles make it easier to reuse and scale configurations across multiple environments.

3. **Variables in Roles:**  
   Customizing roles using `defaults`, `vars`, and command-line overrides (`-e`).

4. **Combining Roles:**  
   Use multiple roles in a single playbook for complex setups.

These exercises lay the foundation for using roles effectively in real-world deployments.