### **Day 5: Lab Series 3 - Understanding and Configuring Execution Strategies**

#### **Objective:**  
This lab introduces participants to execution strategies in Ansible. Participants will understand how `forks`, `serial`, and `throttle` settings affect task execution and learn to configure these strategies for optimal resource usage and control.

---

### **Lab 3.1: Configuring Forks**

#### **Scenario:**  
Optimize parallel task execution by increasing the number of forks.

#### **Steps:**

1. **Default Forks Setting:**
   Run a playbook with the default `forks` setting.
   ```yaml
   ---
   - name: Test Default Forks
     hosts: all
     tasks:
       - name: Ping all hosts
         ping:
   ```

   Command to run:
   ```bash
   ansible-playbook forks_example.yaml -i inventory
   ```

   Observe the speed of execution for multiple hosts.

2. **Increase Forks:**

   Set a higher `forks` value in the playbook execution command:
   ```bash
   ansible-playbook forks_example.yaml -i inventory --forks 10
   ```

3. **Exercise:**  
   - Compare the execution time with different `forks` values (e.g., 1, 5, 10).
   - Test with a larger inventory file to see the impact.

---

### **Lab 3.2: Using Serial Execution**

#### **Scenario:**  
Control task execution by running tasks on a limited number of hosts at a time.

#### **Steps:**

1. **Playbook with `serial`:**

   Save as `serial_example.yaml`:
   ```yaml
   ---
   - name: Execute Tasks Serially
     hosts: all
     serial: 2
     tasks:
       - name: Display Hostname
         debug:
           msg: "Task running on {{ inventory_hostname }}"
   ```

2. **Run the Playbook:**

   ```bash
   ansible-playbook serial_example.yaml -i inventory
   ```

3. **Exercise:**  
   - Modify the `serial` value to `1`, `2`, or `5`, and observe the behavior.
   - Identify scenarios where serial execution might be beneficial (e.g., rolling updates).

---

### **Lab 3.3: Using Throttle for Task Control**

#### **Scenario:**  
Limit concurrent execution of a specific task using the `throttle` keyword.

#### **Steps:**

1. **Playbook with Throttling:**

   Save as `throttle_example.yaml`:
   ```yaml
   ---
   - name: Throttle Specific Task Execution
     hosts: all
     tasks:
       - name: Install Packages
         yum:
           name: httpd
           state: present
         throttle: 3
   ```

2. **Run the Playbook:**

   ```bash
   ansible-playbook throttle_example.yaml -i inventory
   ```

3. **Exercise:**  
   - Experiment with different throttle values (e.g., 1, 3, 5).
   - Compare how throttling differs from `serial`.

---

### **Lab 3.4: Combining Strategies**

#### **Scenario:**  
Use `serial`, `throttle`, and `forks` together to configure complex execution strategies.

#### **Steps:**

1. **Playbook with Multiple Strategies:**

   Save as `combined_example.yaml`:
   ```yaml
   ---
   - name: Test Combined Strategies
     hosts: all
     serial: 2
     tasks:
       - name: Ping Hosts
         ping:

       - name: Deploy Apache
         yum:
           name: httpd
           state: present
         throttle: 1
   ```

2. **Run the Playbook:**

   ```bash
   ansible-playbook combined_example.yaml -i inventory --forks 5
   ```

3. **Exercise:**  
   - Analyze how `serial`, `throttle`, and `forks` interact.
   - Test with a larger inventory file for deeper insights.

---

### **Optional Lab: Debugging Execution Strategies**

#### **Scenario:**  
Test execution strategies in failure scenarios.

1. Modify a playbook to deliberately fail on a few hosts (e.g., by using incorrect package names or unreachable hosts).  
2. Run the playbook with different strategies:
   - `serial`
   - `throttle`
   - `forks`
3. Observe the behavior and learn how each strategy handles errors.

---

### **Key Learning Points:**

1. **Forks:**  
   Determine how to optimize parallel execution for large inventories.

2. **Serial Execution:**  
   Control rolling updates or phased deployments with `serial`.

3. **Throttling:**  
   Limit resource-intensive tasks to a subset of hosts.

4. **Combining Strategies:**  
   Mix execution strategies to balance speed and control in complex deployments.

These exercises empower participants to effectively manage execution flow in diverse scenarios.