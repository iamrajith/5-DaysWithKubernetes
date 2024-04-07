
## Ansible Playbook: Notify and handlers in ansible

### Objective

Let's update the playbook to include a feature that restarts the Apache service in case of any changes in the web content. We'll utilize the notification handlers to achieve this. 

For this demonstration, we'll use Apache running on a CentOS server.

### Instructions

1. **Create the `index.html` File Locally**:
   - Open your terminal or command prompt.
   - Type `vi index.html` and press `ENTER`.
   - You'll enter the `vi` editor. Press `i` to enter insert mode.
   - Copy and paste the following HTML content into the editor:
     ```html
     <!DOCTYPE html>
     <html>
     <head>
         <title>Hello from Ansible on CentOS with Apache!</title>
     </head>
     <body>
         <h1>Welcome to my Ansible-powered website!</h1>
         <p>This is a sample webpage created using Ansible.</p>
     </body>
     </html>
     ```
   - Press `ESC` to exit insert mode.
   - Type `:wq` and press `ENTER` to save and exit.

2. **Create the Ansible Playbook**:
   - Create a new playbook file named `nginx-playbook.yml`:
     ```
     nano nginx-playbook.yml
     ```
   - Add the following content to your `nginx-playbook.yml` file:
     ```yaml
     ---
     - hosts: centos_servers
       become: yes
       tasks:
         - name: Install apache on CentOS
           yum:
             name: httpd
             state: present

         - name: Copy HTML file to the server
           copy:
             src: /path/to/your/local/index.html
             dest: /var/www/html/index.html
           notify:
           - Restart Apache if content changes
         - name: Start apache service
           systemd:
             name: httpd
             enabled: yes
             state: started

       handlers:
         - name: Restart Apache if content changes
           systemd:
             name: httpd
             state: restarted
           listen: "content_changed"
     ```
   - Replace `/path/to/your/local/index.html` with the actual path to your locally created `index.html` file.
   - Save and close the file (if using `nano`, press `CTRL+X`, then `Y`, and `ENTER`).

3. **Run the Playbook**:
   - Execute the playbook on the target CentOS servers:
     ```
     ansible-playbook -i inventory nginx-playbook.yml
     ```
   - Verify that Nginx is installed, the HTML page is accessible, and the content matches what you created locally.


### Notify and Handlers Explained 

1. **Notifies**:
   - In Ansible, a `notify` is a way to trigger a handler when a task changes something.
   - When a task modifies a resource (e.g., installs a package, creates a file), it can notify a specific handler.
   - Handlers are only executed if the task reports a change.
   - Useful for services that need to be restarted after configuration changes (e.g., Nginx, Apache).
   - Example:
     ```yaml
         - name: Copy HTML file to the server
           copy:
             src: /path/to/your/local/index.html
             dest: /var/www/html/index.html
           notify:
           - Restart Apache if content changes
     ```

2. **Handlers**:
   - Handlers are tasks that are only executed when notified by other tasks.
   - They are defined separately from regular tasks.
   - Handlers are typically used to restart services or perform other actions after configuration changes.
   - Example:
     ```yaml
       handlers:
         - name: Restart Apache if content changes
           systemd:
             name: httpd
             state: restarted
           listen: "content_changed"
     ```

For more details, explore additional Ansible concepts in the [official Ansible documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html#handlers-running-operations-on-change). .



## Testing Notify and Handlers

### Objective
In this lab, we'll modify the content of the `index.html` file, rerun the playbook, and observe how the `notify` and `handlers` work when the content changes.

### Instructions

1. **Create the `index.html` File Locally**:
   - Open your terminal or command prompt.
   - Type `vi index.html` and press `ENTER`.
   - You'll enter the `vi` editor. Press `i` to enter insert mode.
   - Copy and paste the following HTML content into the editor:
     ```html
     <!DOCTYPE html>
     <html>
     <head>
         <title>Hello from Ansible on CentOS with Apache!</title>
     </head>
     <body>
         <h1>Welcome to my Ansible-powered website!</h1>
         <p>This is a sample webpage created using Ansible.</p>
         <p>Let us observe how the `notify` and `handlers` work when the content changes.</p>
     </body>
     </html>
     ```
   - Press `ESC` to exit insert mode.
   - Type `:wq` and press `ENTER` to save and exit.

2. **Rerun the Ansible Playbook**:
   - Execute the playbook on the target CentOS servers (replace with your own inventory file and administrative user):
     ```
     ansible-playbook -i inventory nginx-playbook.yml
     ```
   - Verify that httpd is still running and the updated content is displayed when you access the server via a web browser.

3. **Observe the Handler in Action**:
   - Check the status of the Nginx service:
     ```
     systemctl status httpd
     ```
   - If the content has changed, the handler will automatically restart Nginx.

### Conclusion
You've successfully tested the `notify` and `handlers` mechanism in Ansible! The Nginx service should restart automatically when the content changes.

