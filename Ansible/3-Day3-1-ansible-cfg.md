## Ansible Configuration Settings

Let's dive into the most common configuration settings used in the `ansible.cfg` file. This file plays a crucial role in customizing Ansible's behavior. Here are some key points and settings:

1. **Configuration File Locations**:
   - Ansible searches for configuration files in the following order:
     - **Environment Variable (`ANSIBLE_CONFIG`)**: If set, Ansible looks for the path specified in the `ANSIBLE_CONFIG` environment variable.
     - **Current Directory (`ansible.cfg`)**: Ansible checks for an `ansible.cfg` file in the current working directory.
     - **User-Specific (`~/.ansible.cfg`)**: The user-specific configuration file takes precedence over the system-wide configuration. Customize it to your needs.
     - **System-Wide (`/etc/ansible/ansible.cfg`)**: The system-wide configuration file applies to all users.
   - Ansible processes this list and uses the first file found, ignoring the others.

2. **INI Format**:
   - The `ansible.cfg` file follows an INI format.
   - Both the hash sign (`#`) and semicolon (`;`) are allowed as comment markers when the comment starts the line.
   - If the comment is inline with regular values, only the semicolon is allowed to introduce the comment. For example:
     ```
     # Some basic default values...
     inventory = /etc/ansible/hosts  ; This points to the file that lists your hosts
     ```

3. **Generating a Sample `ansible.cfg` File**:
   - You can create a fully commented-out example `ansible.cfg` file using the following commands:
     - Generate a minimal file:
       ```
       $ ansible-config init --disabled > ansible.cfg
       ```
     - Generate a more complete file that includes existing plugins:
       ```
       $ ansible-config init --disabled -t all > ansible.cfg
       ```
   - Use these generated files as starting points to create your own customized `ansible.cfg`.

4. **Security Considerations**:
   - Avoid placing `ansible.cfg` in a world-writable current working directory. Loading a config file from such a directory could create security risks.
   - Restrict access to your Ansible directories to specific users or groups to mitigate this risk.


### Reference 
 [official Ansible documentation](https://docs.ansible.com/ansible/latest/reference_appendices/config.html)