
## Introduction to Ansible Ad-hoc Commands
- **What are Ad-hoc Commands?**
  - Ansible ad hoc commands use the `/usr/bin/ansible` command-line tool to automate a single task on one or more managed nodes.
  - They are quick and easy but not reusable.
  - Ad hoc commands demonstrate the simplicity and power of Ansible.
  - Concepts learned here directly apply to playbook development.

**If you are familiar with Linux, we can compare the Ansible ad hoc commands with one-liner Linux shell commands; and the playbooks to shell script.**

Refer the [official Ansible documentation](https://docs.ansible.com/ansible/latest/command_guide/intro_adhoc.html#intro-adhoc) for more information.

- **Use Cases for Ad-hoc Tasks:**
  - Rebooting servers
  - Managing files
  - Installing packages
  - Managing users and groups
  - Managing services
  - Gathering facts
  - Check mode (dry run)
  - And more!


## Practical Examples of Ansible Ad-hoc Commands

1. **Ping All Hosts:**
   - Verify connectivity to all hosts.
   - Command: `$ ansible all -m ping`
   - Sample Output:
     ```
     server1 | SUCCESS => {
         "changed": false,
         "ping": "pong"
     }
     server2 | SUCCESS => {
         "changed": false,
         "ping": "pong"
     }
     ```
*(This is not the OS ping command,it is an [Ansible module named ping)*](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/ping_module.html)*


2. **Check Uptime:**
   - Get uptime information from hosts.
   - Command: `$ ansible all -a "uptime"`
   - Sample Output:
     ```
     server1 | CHANGED | rc=0 >>
     18:30:00 up 10 days,  2:45,  0 users,  load average: 0.00, 0.01, 0.05

     server2 | CHANGED | rc=0 >>
     18:30:00 up 5 days,  3:20,  0 users,  load average: 0.02, 0.03, 0.05
     ```

3. **Memory Usage:**
   - Check free memory on hosts.
   - Command: `$ ansible all -a "free -m"`
   - Sample Output:
     ```
     server1 | CHANGED | rc=0 >>
                   total        used        free      shared  buff/cache   available
     Mem:           1994         123        1671           0         199        1769
     Swap:          2047           0        2047

     server2 | CHANGED | rc=0 >>
                   total        used        free      shared  buff/cache   available
     Mem:           1994         124        1670           0         199        1768
     Swap:          2047           0        2047
     ```

4. **Create a Unix User:**
   - Create a user named "myuser."
   - Command: `$ ansible all -a "useradd myuser"`
   - Sample Output:
     ```
     server1 | CHANGED | rc=0 >>
     (no output)

     server2 | CHANGED | rc=0 >>
     (no output)
   
     ```
*(The user creation require root privillage in the managed nodes, if not the command will fail)*

5. **Check User ID (UID):**
   - Verify the user ID for "myuser."
   - Command: `$ ansible all -a "id myuser"`
   - Sample Output:
     ```
     server1 | CHANGED | rc=0 >>
     uid=1001(myuser) gid=1001(myuser) groups=1001(myuser)

     server2 | CHANGED | rc=0 >>
     uid=1001(myuser) gid=1001(myuser) groups=1001(myuser)
     ```

6. **Create a Directory Using the File Module:**
   - Create a directory named "/etc/some_directory."
   - Command: `$ ansible all -m file -a "path=/etc/some_directory state=directory mode=0755"`
   - Note: The Ansible `file` module is used to manage files and directories. It allows you to create, delete, modify permissions, copy, touch, or manipulate file ownership¹.
Note: *(We used an Ansible module named file. We will discuss modules later in the session. If you want to know more about the file module,)*[please refer to the official documentation.](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_module.html)