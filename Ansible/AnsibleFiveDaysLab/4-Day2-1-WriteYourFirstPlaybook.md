### **Hands-on learning with Ansible playbooks**

This lab exercise focuses on hands-on learning with Ansible playbooks, enabling participants to gain practical experience in writing, debugging, and executing tasks on remote systems. It includes step-by-step scenarios to build confidence in using Ansible modules and workflows. The exercises are designed for **Amazon Linux** environments and gradually progress in complexity to ensure thorough understanding.

#### **Objectives:**
1. **Exercise 1:** Explore the `debug` module to track task execution and understand the dynamic flow of playbooks.  
2. **Exercise 2:** Learn to create directories, manipulate files, copy data, and verify changes using appropriate Ansible modules (`file`, `lineinfile`, `shell`, etc.).  
3. **Exercise 3:** Configure an Apache web server (`httpd`), modify its content, and apply changes dynamically to demonstrate service management and file operations.  
4. **Exercise 4:** Practice reverting changes by stopping services, uninstalling packages, and cleaning up files using Ansible.

#### **Key Learning Outcomes:**
- Gain confidence in writing and executing Ansible playbooks.
- Understand module selection and task sequencing for real-world scenarios.
- Develop troubleshooting skills to identify and resolve issues in playbooks.
- Learn best practices for configuration management and cleanup.
---

### **Exercise 1: Debug Module Playbook**  

#### **Objective:**  
Learn to use the `debug` module to display dynamic messages for tracking playbook execution.

#### **Playbook Details:**  
1. **Location:** Save the playbook as `~/day2/debug_playbook.yml`.  
2. **Playbook Structure:**  
   - **Task 1:** Display: `"First task executed on corresponding hostname: <inventory_hostname>"`.  
   - **Task 2:** Display: `"Second task executed, the uptime of the system is '<actual uptime>'."`  
     - **Hint:** Use `ansible_facts['uptime']`.  
   - **Task 3:** Display: `"Third task executed, server time is '<actual server time>'."`  
     - **Hint:** Use `ansible_date_time`.  
   - **Task 4:** Display: `"Final task executed. All tasks on '<inventory_hostname>' completed."`  

#### **Expected Outcome:**  
Participants will see debug messages in the output for each task executed, showcasing dynamic information.

---

### **Exercise 2: Task-Based Playbook Creation**  

#### **Objective:**  
Understand how to build playbooks with multiple tasks, ensuring logical flow and using appropriate modules.

#### **Instructions:**  
1. **Location:** Save the playbook as `~/day2/task_playbook.yml`.  
2. **Playbook Structure:**  
   - **Task 1:** Create a directory under `/tmp/<username>` (replace `<username>` with the participant’s username).  
     - **Hint:** Use the `file` module.  
   - **Task 2:** Use the `lineinfile` module to create a file in `/tmp/<username>` with the content: `"File creation completed successfully."`.  
   - **Task 3:** Copy a dummy file (`dummy.txt`) from the jump server to the newly created directory.  
     - Ensure participants create the `dummy.txt` file on the jump server beforehand.  
   - **Task 4:** Use the `shell` module to execute `ls -l` on the directory and verify the file's presence.  
   - **Task 5:** Print a message: `"All 5 tasks completed successfully."`  

#### **Additional Instructions:**  
- **No Playbook Solution Provided:** Participants must identify and use appropriate modules for each task.  
- Encourage participants to explore module documentation if unsure.  

---

### **Optional Lab Exercises**  

#### **Exercise 3: Apache Setup Playbook Enhancement**  

1. **Objective:**  
   Modify the existing `apache_setup.yml` playbook to:  
   - **Step 1:** Request the public IP address of the VM and verify the webpage's accessibility.  
   - **Step 2:** If the webpage doesn’t display, add a task to start the Apache service.  
     - **Hint:** Use the `service` or `systemd` module.  
   - **Step 3:** Modify the `/var/www/html/index.html` to add a second line with content: `"Modification with lineinfile worked successfully."`.  
     - **Hint:** Use the `lineinfile` module.  
   - **Step 4:** Add a step to restart the Apache service after the modification.  

2. **Instructions:**  
   - Do not provide exact steps; instead, encourage participants to write tasks by exploring module documentation.  

---

#### **Exercise 4: Revert Changes**  

1. **Objective:**  
   Write a new playbook to revert all changes made by `apache_setup.yml`.  

2. **Instructions:**  
   - Reverse the following changes:  
     - Remove the second line added to `/var/www/html/index.html`.  
     - Stop and disable the Apache service.  
     - Optionally, uninstall the Apache package.  
   - **Hint:** Encourage participants to determine the task order themselves.  

3. **Expected Outcome:**  
   - The Apache service should be stopped and disabled.  
   - All modifications to `index.html` should be reverted.  
   - Optionally, Apache should be uninstalled.

---

### **Key Learning Outcomes:**  
- Participants will develop a structured approach to writing playbooks.  
- They will gain hands-on experience with frequently used modules (`file`, `lineinfile`, `service`, etc.).  
- Troubleshooting and logical thinking will improve as they solve real-world scenarios without direct guidance.  
