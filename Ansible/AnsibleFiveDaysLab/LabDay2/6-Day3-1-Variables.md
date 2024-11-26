
### **Lab Exercise: Types of Variables and Their Hierarchy**  

#### **Objective:**  
Understand the variable precedence hierarchy in Ansible using **group variables**, **host variables**, **play variables**, **task variables**, and **extra vars** in a step-by-step manner.

---

### **Lab 1.1: Group Variables in Inventory**  

#### **Scenario:**  
Define **group variables** in the inventory file and observe how they are resolved.

---

#### **Steps:**  

1. **Define Inventory with Group Variables:**  

   - Create `~/day3/inventory.ini` with the following content:  
     ```ini
     [web_servers]
     web1 ansible_host=192.168.1.10 ansible_user=ec2-user
     web2 ansible_host=192.168.1.11 ansible_user=ec2-user

     [web_servers:vars]
     website_port=8080
     website_content="Content from group variables"
     ```

2. **Create the Playbook:**  

   - Save as `~/day3/group_variables.yaml`:  
     ```yaml
     - name: Group Variables Demo
       hosts: web_servers
       tasks:
         - name: Display website_port from group variables
           debug:
             msg: "Website Port: {{ website_port }}"

         - name: Display website_content from group variables
           debug:
             msg: "Website Content: {{ website_content }}"
     ```

3. **Run the Playbook:**  

   - Execute the playbook:  
     ```bash
     ansible-playbook ~/day3/group_variables.yaml -i ~/day3/inventory.ini
     ```

4. **Expected Output:**  

   - The values of `website_port` and `website_content` are resolved from the group variables in the inventory file.

---

### **Lab 1.2: Host Variables in Inventory**  

#### **Scenario:**  
Add **host variables** to the inventory and observe how they override group variables.

---

#### **Steps:**  

1. **Modify the Inventory File:**  

   - Update `inventory.ini` to include host-specific variables:  
     ```ini
     [web_servers]
     web1 ansible_host=192.168.1.10 ansible_user=ec2-user website_port=9090
     web2 ansible_host=192.168.1.11 ansible_user=ec2-user

     [web_servers:vars]
     website_port=8080
     website_content="Content from group variables"
     ```

2. **Modify the Playbook (optional):**  

   - Use the same playbook `group_variables.yaml` from Lab 1.1.  

3. **Run the Playbook:**  

   - Execute the playbook and observe the output:  
     ```bash
     ansible-playbook ~/day3/group_variables.yaml -i ~/day3/inventory.ini
     ```

4. **Expected Output:**  

   - `web1` should display `website_port=9090` (host variable), while `web2` uses `website_port=8080` (group variable).  

---

### **Lab 1.3: Play and Task Variables**  

#### **Part A: Using Play Variables**  

1. **Modify the Playbook:**  

   - Update the playbook to include play variables:  
     ```yaml
     - name: Play Variables Demo
       hosts: web_servers
       vars:
         website_port: 10010
         website_content: "Content from play variables"
       tasks:
         - name: Display website_port from play variables
           debug:
             msg: "Website Port: {{ website_port }}"

         - name: Display website_content from play variables
           debug:
             msg: "Website Content: {{ website_content }}"
     ```

2. **Run the Playbook:**  

   - Execute the playbook and observe how play variables override group and host variables:  
     ```bash
     ansible-playbook ~/day3/group_variables.yaml -i ~/day3/inventory.ini
     ```

---

#### **Part B: Using Task Variables**  

1. **Add Task-Level Variables:**  

   - Update the playbook:  
     ```yaml
     - name: Task Variables Demo
       hosts: web_servers
       vars:
         website_port: 10010
         website_content: "Content from play variables"
       tasks:
         - name: Override website_content at the task level
           debug:
             msg: "Task-Level Override: Website Content is '{{ website_content }}'"
           vars:
             website_content: "Task-level override content"

         - name: Display website_port (play variable remains unchanged)
           debug:
             msg: "Website Port: {{ website_port }}"
     ```

2. **Run the Playbook:**  

   - Observe how task variables override play variables for the specific task:  
     ```bash
     ansible-playbook ~/day3/group_variables.yaml -i ~/day3/inventory.ini
     ```

---

### **Lab 1.4: Extra Vars Precedence**  

#### **Scenario:**  
Use **extra vars** with the `-e` option to override all other variables.

---

#### **Steps:**  

1. **Execute the Playbook with Extra Vars:**  

   - Use the following command:  
     ```bash
     ansible-playbook ~/day3/group_variables.yaml -i ~/day3/inventory.ini \
       --extra-vars "website_port=12345 website_content='Content from extra vars'"
     ```

2. **Analyze Results:**  

   - Observe how `website_port` and `website_content` are resolved from extra vars, overriding all other variable sources (inventory, group, host, play, and task variables).

---

### **Outcome of Labs:**  

1. Participants will understand the **variable precedence hierarchy**:  
   - Extra vars (`-e`) > Task vars > Play vars > Host vars > Group vars > Inventory vars > Default vars.  

2. Participants will explore variable resolution dynamically by modifying or adding variables at different levels and observing their behavior.  

This step-by-step progression simplifies understanding variable precedence in Ansible.