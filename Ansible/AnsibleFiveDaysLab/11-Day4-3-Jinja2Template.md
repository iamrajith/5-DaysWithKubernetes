### **Day 4: Lab Series 3 - Dynamic Configurations with Jinja2 Templates**

#### **Objective:**  
This lab introduces participants to creating and utilizing Jinja2 templates for dynamic configurations in Ansible playbooks. By the end of these exercises, participants will understand how to use variables, loops, and conditionals within templates to generate configuration files dynamically.

---

### **Lab 3.1: Basic Jinja2 Template for Configuration**

#### **Scenario:**  
Generate an Nginx configuration file dynamically using a Jinja2 template.

---

#### **Steps:**

1. **Prepare the Template File:**

   - Create the template file `nginx.conf.j2` in the `~/day4/templates/` directory:
     ```jinja2
     server {
         listen 80;
         server_name {{ inventory_hostname }};
         root /var/www/{{ web_root }};

         location / {
             index index.html;
         }
     }
     ```

2. **Create the Playbook:**

   - Save as `~/day4/nginx_config.yaml`:
     ```yaml
     - name: Generate Nginx Configuration File
       hosts: localhost
       become: yes
       vars:
         web_root: html
       tasks:
         - name: Ensure the Nginx configuration directory exists
           file:
             path: /etc/nginx/conf.d
             state: directory

         - name: Generate Nginx configuration file from template
           template:
             src: ~/day4/templates/nginx.conf.j2
             dest: /etc/nginx/conf.d/default.conf

         - name: Restart Nginx to apply configuration
           service:
             name: nginx
             state: restarted
     ```

3. **Run the Playbook:**

   ```bash
   ansible-playbook ~/day4/nginx_config.yaml
   ```

4. **Verify Output:**

   - Check `/etc/nginx/conf.d/default.conf` to ensure it has been correctly generated using the template.

---

### **Lab 3.2: Using Host Variables with Templates**

#### **Scenario:**  
Generate different Apache virtual host configurations for multiple servers using Jinja2 templates.

---

#### **Steps:**

1. **Update the Inventory File:**

   - Define host-specific variables:
     ```ini
     [web]
     web1 ansible_host=192.168.1.101 web_port=8080 web_root=site1
     web2 ansible_host=192.168.1.102 web_port=9090 web_root=site2
     ```

2. **Prepare the Template File:**

   - Save as `apache_vhost.conf.j2` in the `~/day4/templates/` directory:
     ```jinja2
     <VirtualHost *:{{ web_port }}>
         ServerName {{ inventory_hostname }}
         DocumentRoot "/var/www/{{ web_root }}"
         ErrorLog /var/log/httpd/{{ inventory_hostname }}_error.log
         CustomLog /var/log/httpd/{{ inventory_hostname }}_access.log combined
     </VirtualHost>
     ```

3. **Create the Playbook:**

   - Save as `~/day4/apache_config.yaml`:
     ```yaml
     - name: Generate Apache Virtual Host Configurations
       hosts: web
       become: yes
       tasks:
         - name: Ensure the Apache configuration directory exists
           file:
             path: /etc/httpd/conf.d
             state: directory

         - name: Generate virtual host configuration from template
           template:
             src: ~/day4/templates/apache_vhost.conf.j2
             dest: /etc/httpd/conf.d/{{ inventory_hostname }}.conf

         - name: Restart Apache to apply configurations
           service:
             name: httpd
             state: restarted
     ```

4. **Run the Playbook:**

   ```bash
   ansible-playbook ~/day4/apache_config.yaml
   ```

5. **Verify Output:**

   - Check `/etc/httpd/conf.d/` for separate configuration files for each host.  
   - Validate the configurations by accessing the web servers using their respective ports.

---

### **Lab 3.3: Conditional Rendering in Templates**

#### **Scenario:**  
Create an SSH banner message file dynamically, varying the content based on the host’s operating system.

---

#### **Steps:**

1. **Prepare the Template File:**

   - Save as `ssh_banner.j2` in the `~/day4/templates/` directory:
     ```jinja2
     {% if ansible_distribution == "Amazon Linux" %}
     Welcome to the Amazon Linux Server: {{ inventory_hostname }}
     {% elif ansible_distribution == "Ubuntu" %}
     Welcome to the Ubuntu Server: {{ inventory_hostname }}
     {% else %}
     Welcome to the Server: {{ inventory_hostname }}
     {% endif %}
     Unauthorized access is prohibited.
     ```

2. **Create the Playbook:**

   - Save as `~/day4/ssh_banner.yaml`:
     ```yaml
     - name: Generate SSH Banner Message
       hosts: localhost
       become: yes
       tasks:
         - name: Generate SSH banner file from template
           template:
             src: ~/day4/templates/ssh_banner.j2
             dest: /etc/issue.net

         - name: Enable SSH banner
           lineinfile:
             path: /etc/ssh/sshd_config
             regexp: '^#?Banner'
             line: 'Banner /etc/issue.net'

         - name: Restart SSH service
           service:
             name: sshd
             state: restarted
     ```

3. **Run the Playbook:**

   ```bash
   ansible-playbook ~/day4/ssh_banner.yaml
   ```

4. **Verify Output:**

   - Check `/etc/issue.net` for the dynamically rendered banner message.  
   - Verify that the SSH banner displays on login.

---

### **Optional Lab: Nested Loops and Templates**

#### **Scenario:**  
Create user-specific directories and permissions dynamically using a template with nested loops.

---

1. **Prepare the Template File:**

   - Save as `user_dirs.j2` in the `~/day4/templates/` directory:
     ```jinja2
     {% for user, dirs in user_data.items() %}
     User: {{ user }}
     Directories:
     {% for dir in dirs %}
     - {{ dir }}
     {% endfor %}
     {% endfor %}
     ```

2. **Create the Playbook:**

   - Save as `~/day4/user_dirs.yaml`:
     ```yaml
     - name: Generate User Directories from Template
       hosts: localhost
       become: yes
       vars:
         user_data:
           alice:
             - /home/alice/docs
             - /home/alice/projects
           bob:
             - /home/bob/docs
             - /home/bob/music
       tasks:
         - name: Create directories for users
           file:
             path: "{{ item.1 }}"
             state: directory
             owner: "{{ item.0 }}"
           with_subelements:
             - "{{ user_data.items() }}"
             - 1
     ```

3. **Run the Playbook:**  

   ```bash
   ansible-playbook ~/day4/user_dirs.yaml
   ```

4. **Verify Output:**  

   - Check if the directories are created for each user based on the variables provided.

---

### **Key Learning Points:**

1. **Templates and Variables:**  
   - Use Jinja2 to generate configurations dynamically based on variables and facts.  

2. **Conditional Logic:**  
   - Utilize conditionals in templates to handle different scenarios.  

3. **Advanced Scenarios:**  
   - Create reusable and complex configurations using loops and nested data structures.  

4. **Optional Challenges:**  
   - Add more variables, conditions, or loops to explore dynamic configurations further.  

This lab introduces participants to the power of Jinja2 templates for managing dynamic configurations, a key skill in scalable automation.