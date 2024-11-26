### **Day 5: Lab Exercise - Using Include and Import for Task and Play Segregation**

#### **Objective:**  
This lab introduces participants to **modularizing Ansible playbooks** using the `include_tasks`, `import_tasks`, and `import_playbook` directives. Participants will learn how to segregate tasks and plays to improve playbook reusability, readability, and maintainability.

---

### **Lab 1.1: Using `include_tasks` for Dynamic Task Inclusion**

#### **Scenario:**  
Dynamically include tasks based on a variable (e.g., install Apache on one host group and Nginx on another).

#### **Steps:**

1. **Create Task Files:**

   - Create `~/day5/tasks/install_apache.yaml`:
     ```yaml
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

   - Create `~/day5/tasks/install_nginx.yaml`:
     ```yaml
     - name: Install Nginx
       yum:
         name: nginx
         state: present

     - name: Start and enable Nginx
       service:
         name: nginx
         state: started
         enabled: true
     ```

2. **Create the Playbook:**

   - Save as `~/day5/include_tasks_playbook.yaml`:
     ```yaml
     - name: Include Tasks Dynamically
       hosts: all
       vars:
         web_server: apache
       tasks:
         - name: Dynamically include tasks based on web_server variable
           include_tasks: "tasks/install_{{ web_server }}.yaml"
     ```

3. **Run the Playbook:**

   ```bash
   ansible-playbook ~/day5/include_tasks_playbook.yaml
   ```

4. **Exercise:**

   - Change the `web_server` variable to `nginx` and re-run the playbook.  

---

### **Lab 1.2: Using `import_tasks` for Static Task Inclusion**

#### **Scenario:**  
Include static tasks for system setup (e.g., update packages and install basic tools).

#### **Steps:**

1. **Create Task Files:**

   - Create `~/day5/tasks/update_system.yaml`:
     ```yaml
     - name: Update all packages
       yum:
         name: "*"
         state: latest
     ```

   - Create `~/day5/tasks/install_tools.yaml`:
     ```yaml
     - name: Install basic tools
       yum:
         name:
           - vim
           - curl
           - git
         state: present
     ```

2. **Create the Playbook:**

   - Save as `~/day5/import_tasks_playbook.yaml`:
     ```yaml
     - name: Import Tasks for System Setup
       hosts: all
       tasks:
         - import_tasks: tasks/update_system.yaml
         - import_tasks: tasks/install_tools.yaml
     ```

3. **Run the Playbook:**

   ```bash
   ansible-playbook ~/day5/import_tasks_playbook.yaml
   ```

---

### **Lab 1.3: Using `import_playbook` for Playbook Segregation**

#### **Scenario:**  
Split a large playbook into smaller modular playbooks and include them.

#### **Steps:**

1. **Create Smaller Playbooks:**

   - Save as `~/day5/playbooks/webserver_setup.yaml`:
     ```yaml
     - name: Web Server Setup
       hosts: webservers
       tasks:
         - import_tasks: tasks/install_apache.yaml
     ```

   - Save as `~/day5/playbooks/dbserver_setup.yaml`:
     ```yaml
     - name: Database Server Setup
       hosts: dbservers
       tasks:
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

2. **Create the Master Playbook:**

   - Save as `~/day5/import_playbook_master.yaml`:
     ```yaml
     - import_playbook: playbooks/webserver_setup.yaml
     - import_playbook: playbooks/dbserver_setup.yaml
     ```

3. **Run the Master Playbook:**

   ```bash
   ansible-playbook ~/day5/import_playbook_master.yaml
   ```

4. **Exercise:**  
   - Modify the `dbserver_setup.yaml` playbook to include tasks for creating a database and a user.

---

### **Optional Lab 1.4: Dynamic Web and Database Role Assignment**

1. Use `group_vars` or inventory variables to dynamically assign `web_server` and `db_server` roles.
2. Create a playbook to assign tasks based on these variables.

---

### **Key Learning Points:**

1. **`include_tasks` vs. `import_tasks`:**  
   - `include_tasks` allows dynamic task inclusion during runtime.  
   - `import_tasks` includes tasks statically during playbook parse time.

2. **Modular Playbooks with `import_playbook`:**  
   - Segregating large playbooks into smaller, reusable components.  

3. **Improved Maintainability:**  
   - Modularization leads to better organization and easier troubleshooting.

These exercises introduce participants to modular playbook designs, preparing them for advanced role management and large-scale deployments.