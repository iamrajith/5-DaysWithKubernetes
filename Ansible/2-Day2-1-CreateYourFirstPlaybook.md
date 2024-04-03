## Create your first playbook

### Objective
Create an Ansible playbook that accomplishes the following tasks on a set of target servers:
1. Install Nginx.
2. Configure a simple HTML page to display "Hello from Ansible!"

### Instructions
1. **Set Up Your Environment**:
   - Ensure you have Ansible installed on your control machine.
   - Create a directory for your practice playbooks (e.g., `~/ansible-practice`).
   - Copy your existing inventory file (if you have one) into this directory.

2. **Create Your Nginx Playbook**:
   - Create a new playbook file named `nginx-playbook.yml` in your `ansible-practice` directory:
     ```
     vi ~/ansible-practice/nginx-playbook.yml
     ```
   - Add the following content to your `nginx-playbook.yml` file:
     ```yaml
     ---
     - hosts: webservers
       become: yes
       tasks:
         - name: Install Nginx
           apt:
             name: nginx
             state: present

         - name: Create HTML file
           copy:
             content: "<html><body><h1>Hello from Ansible!</h1></body></html>"
             dest: /var/www/html/index.html
     ```
   - Save and close the file.

3. **Run the Playbook**:
   - Execute the playbook on the target servers (replace with your own inventory file and administrative user):
     ```
     ansible-playbook -i inventory nginx-playbook.yml -u sammy
     ```
   - Verify that Nginx is installed and the HTML page is accessible by opening a web browser and navigating to `http://your-server-ip`.


You've successfully created an Ansible playbook to set up an Nginx web server! 

Let's explore further, let us see how to do the same in **CentOS**.

### CentOS Example: Install Nginx

```yaml
---
- hosts: centos_servers
  become: yes
  tasks:
    - name: Install Nginx on CentOS
      yum:
        name: nginx
        state: present

    - name: Create HTML file
      copy:
        content: "<html><body><h1>Hello from Ansible on CentOS!</h1></body></html>"
        dest: /usr/share/nginx/html/index.html
```
Here in **Ubuntu**, we used the **apt** module, for **Centos** it was the **yum** module. 

Let's enhance the CentOS playbook to start the Nginx service and enable it for auto-start.

```yaml
---
- hosts: centos_servers
  become: yes
  tasks:
    - name: Install Nginx on CentOS
      yum:
        name: nginx
        state: present

    - name: Create HTML file
      copy:
        content: "<html><body><h1>Hello from Ansible on CentOS!</h1></body></html>"
        dest: /usr/share/nginx/html/index.html

    - name: Start Nginx service
      systemd:
        name: nginx
        enabled: yes
        state: started
```

Explanation:
1. The `systemd` module is used to manage services. We added an entry to start the Nginx service (`nginx`).
2. The `enabled: yes` line ensures the Nginx start on system boot.

Now, when you run this playbook, 

- Install Nginx
- Copy the HTML content
- Start the Nginx service
- Enable it for auto-start

Remember to adapt the instructions to your specific environment. Happy automating! 😊

For more details, explore [Ansible playbooks and modules in the official Ansible documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html).