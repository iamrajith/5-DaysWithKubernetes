### ****Lab Exercise:- Notify Handlers for Event-Driven Actions**

#### **Objective:**  
Participants will learn to use **notify and handlers** to implement event-driven actions in Ansible. The labs will demonstrate triggering handlers based on task outcomes, including optional exercises for advanced learning.

---

### **Lab 1.1: Basic Notify and Handlers**

#### **Scenario:**  
You want to install Apache, start the service, and restart it only if the configuration file is updated.

---

#### **Steps:**

1. **Create the Playbook:**

   - Save as `~/day4/notify_basic.yaml`:  
     ```yaml
     - name: Notify Handlers Demo - Basic
       hosts: web_servers
       tasks:
         - name: Install Apache
           yum:
             name: httpd
             state: present

         - name: Update Apache Configuration
           copy:
             src: ~/day4/httpd.conf
             dest: /etc/httpd/conf/httpd.conf
           notify: Restart Apache

         - name: Start Apache
           service:
             name: httpd
             state: started

       handlers:
         - name: Restart Apache
           service:
             name: httpd
             state: restarted
     ```

2. **Create a Sample Config File:**

   - Save `httpd.conf` in `~/day4/`:  
     ```plaintext
     # Basic Apache Config
     ServerName localhost
     ```

3. **Run the Playbook:**  

   ```bash
   ansible-playbook ~/day4/notify_basic.yaml -i ~/day3/inventory.ini
   ```

4. **Expected Output:**  

   - Apache is installed.  
   - If `httpd.conf` is updated, the "Restart Apache" handler is triggered.  

---

### **Lab 1.2: Multiple Handlers**

#### **Scenario:**  
Expand the setup to include additional services like enabling the firewall and restarting Apache only after the firewall is configured.

---

#### **Steps:**

1. **Create the Playbook:**

   - Save as `~/day4/multiple_handlers.yaml`:  
     ```yaml
     - name: Notify Handlers Demo - Multiple Handlers
       hosts: web_servers
       tasks:
         - name: Install Apache
           yum:
             name: httpd
             state: present

         - name: Configure Firewall for HTTP
           firewalld:
             port: 80/tcp
             permanent: true
             state: enabled
           notify: Reload Firewall

         - name: Update Apache Configuration
           copy:
             src: ~/day4/httpd.conf
             dest: /etc/httpd/conf/httpd.conf
           notify:
             - Restart Apache
             - Log Apache Update

         - name: Start Apache
           service:
             name: httpd
             state: started

       handlers:
         - name: Restart Apache
           service:
             name: httpd
             state: restarted

         - name: Reload Firewall
           service:
             name: firewalld
             state: restarted

         - name: Log Apache Update
           debug:
             msg: "Apache configuration has been updated and restarted."
     ```

2. **Run the Playbook:**  

   ```bash
   ansible-playbook ~/day4/multiple_handlers.yaml -i ~/day3/inventory.ini
   ```

3. **Expected Output:**  

   - The firewall configuration is reloaded if HTTP is enabled.  
   - Apache restarts only if its configuration file is updated.  

---

### **Lab 1.3: Conditional Notifications**  

#### **Scenario:**  
Add a conditional notification for restarting Apache only if the server has more than 1GB of free memory.

---

#### **Steps:**

1. **Create the Playbook:**

   - Save as `~/day4/conditional_notify.yaml`:  
     ```yaml
     - name: Notify Handlers Demo - Conditional Notifications
       hosts: web_servers
       tasks:
         - name: Check Free Memory
           shell: free -m | awk '/^Mem/ { print $4 }'
           register: free_memory

         - name: Install Apache
           yum:
             name: httpd
             state: present

         - name: Update Apache Configuration
           copy:
             src: ~/day4/httpd.conf
             dest: /etc/httpd/conf/httpd.conf
           notify: Restart Apache

         - name: Trigger Restart if Free Memory > 1024MB
           debug:
             msg: "Memory is sufficient. Triggering restart."
           when: free_memory.stdout | int > 1024
           notify: Restart Apache

       handlers:
         - name: Restart Apache
           service:
             name: httpd
             state: restarted
     ```

2. **Run the Playbook:**  

   ```bash
   ansible-playbook ~/day4/conditional_notify.yaml -i ~/day3/inventory.ini
   ```

3. **Expected Output:**  

   - Apache restarts only if there’s more than 1GB of free memory.  

---

### **Optional Lab: Chain Multiple Handlers**  

#### **Scenario:**  
Update a custom HTML file and restart Apache only if the update is successful.

---

1. **Create the Playbook:**

   - Save as `~/day4/chain_handlers.yaml`:  
     ```yaml
     - name: Chain Multiple Handlers Demo
       hosts: web_servers
       tasks:
         - name: Install Apache
           yum:
             name: httpd
             state: present

         - name: Update Website Content
           copy:
             src: ~/day4/index.html
             dest: /var/www/html/index.html
           notify: Verify Content Update

       handlers:
         - name: Verify Content Update
           shell: "grep 'Welcome' /var/www/html/index.html"
           register: content_check
           changed_when: content_check.rc == 0
           notify: Restart Apache

         - name: Restart Apache
           service:
             name: httpd
             state: restarted
     ```

2. **Create the `index.html` File:**

   - Save in `~/day4/`:  
     ```html
     <html>
     <body>
       <h1>Welcome to Ansible Training!</h1>
     </body>
     </html>
     ```

3. **Run the Playbook:**  

   ```bash
   ansible-playbook ~/day4/chain_handlers.yaml -i ~/day3/inventory.ini
   ```

4. **Expected Output:**  

   - Apache restarts only if `index.html` contains "Welcome."

---

### **Key Learning Points:**  

1. **Notify and Handlers:**  
   - Trigger handlers based on task outcomes.
   - Chain multiple handlers for dependent actions.

2. **Conditional Notifications:**  
   - Use conditional logic to control when handlers are triggered.

3. **Optional Exercises:**  
   - Allow participants to explore chaining and advanced notifications.

This lab ensures a hands-on understanding of **notify and handlers** in Ansible with real-world scenarios and customization opportunities.