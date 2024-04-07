## Ansible Playbook:Basic conditionals with when

Let’s explore the usage of conditionals in Ansible by combining both playbooks. We’ll use the when condition based on the OS: for CentOS, we’ll install the package using yum, and for Ubuntu, we’ll use apt.

```yaml
---
- hosts: webservers
  become: yes
  tasks:
    - name: Install Nginx on CentOS
      yum:
        name: nginx
        state: present
      when: ansible_distribution == 'CentOS'

    - name: Install Nginx on Ubuntu
      apt:
        name: nginx
        state: present
      when: ansible_distribution == 'Ubuntu'

    - name: Create HTML file
      copy:
        content: "<html><body><h1>Hello from Ansible!</h1></body></html>"
        dest: /var/www/html/index.html

    - name: Start Nginx service
      systemd:
        name: nginx
        enabled: yes
        state: started

  handlers:
    - name: Restart Nginx if content changes
      systemd:
        name: nginx
        state: restarted
      listen: "content_changed"
```


- The first task installs Nginx using `yum` if the OS is CentOS.
- The second task installs Nginx using `apt` if the OS is Ubuntu.
- The HTML file creation task remains the same for both OS.
- The handler ensures that Nginx is restarted if the content changes.

Now, when you run this playbook, it will set up Nginx based on the OS type and handle content changes gracefully.
