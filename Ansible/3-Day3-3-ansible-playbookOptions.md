## Ansible Playbook Options


Let's explore the various options available when running an Ansible playbook using the `ansible-playbook` command.


1. **`--version`**:
   - Displays the Ansible version information.
   - Example:
     ```
     ansible-playbook --version
     ```

2. **`--syntax-check`**:
   - Validates the syntax of your playbook without executing it.
   - Useful for catching syntax errors before running the playbook.
   - Example:
     ```
     ansible-playbook myplaybook.yml --syntax-check
     ```

3. **`-C` or `--check`**:
   - Performs a dry run (check mode) without making any changes.
   - Simulates playbook execution and reports what changes would occur.
   - Useful for testing before applying changes in production.
   - Example:
     ```
     ansible-playbook myplaybook.yml --check
     ```

4. **`-i` or `--inventory` or `--inventory-file`**:
   - Specifies the inventory file (hosts file) to use.
   - By default, Ansible looks for the inventory file at `/etc/ansible/hosts`.
   - Example:
     ```
     ansible-playbook -i my_inventory.ini myplaybook.yml
     ```

5. **`--list-hosts`**:
   - Outputs a list of matching hosts from the inventory.
   - Does not execute any tasks; useful for host discovery.
   - Example:
     ```
     ansible-playbook myplaybook.yml --list-hosts
     ```

6. **`--list-tasks`**:
   - Lists all tasks defined in the playbook.
   - Useful for understanding the playbook structure.
   - Example:
     ```
     ansible-playbook myplaybook.yml --list-tasks
     ```

7. **`--list-tags`**:
   - Lists all available tags defined in the playbook.
   - Helps identify which tasks are associated with specific tags.
   - Example:
     ```
     ansible-playbook myplaybook.yml --list-tags
     ```

8. **`-f <FORKS>` or `--forks <FORKS>`**:
   - Sets the number of parallel processes (concurrent connections).
   - Controls how many hosts Ansible manages simultaneously.
   - Example:
     ```
     ansible-playbook -f 10 myplaybook.yml
     ```

9. **`-T <TIMEOUT>` or `--timeout <TIMEOUT>`**:
   - Sets the SSH connection timeout (in seconds).
   - Useful for adjusting the connection timeout threshold.
   - Example:
     ```
     ansible-playbook -T 30 myplaybook.yml
     ```

10. **`-k` or `--ask-pass`**:
    - Prompts for the SSH password (deprecated; use SSH keys instead).
    - Example:
      ```
      ansible-playbook -k myplaybook.yml
      ```

11. **`-K` or `--ask-become-pass`**:
    - Prompts for the privilege escalation (become) password.
    - Useful when using `--become` (sudo) for privilege escalation.
    - Example:
      ```
      ansible-playbook -K myplaybook.yml
      ```

12. **`-u <REMOTE_USER>` or `--user <REMOTE_USER>`**:
    - Specifies the remote user for SSH connections.
    - Example:
      ```
      ansible-playbook -u myuser myplaybook.yml
      ```

13. **`-b` or `--become`**:
    - Enables privilege escalation (become) for tasks.
    - Equivalent to using `sudo` or `su`.
    - Example:
      ```
      ansible-playbook -b myplaybook.yml
      ```

14. **`--become-user <BECOME_USER>`**:
    - Specifies the user for privilege escalation (become).
    - Useful when becoming a different user (e.g., root).
    - Example:
      ```
      ansible-playbook --become-user root myplaybook.yml
      ```

15. **`--step`**:
    - Executes tasks interactively, prompting for confirmation.
    - Useful for debugging and understanding task execution.
    - Example:
      ```
      ansible-playbook --step myplaybook.yml
      ```

16. **`-l <SUBSET>` or `--limit <SUBSET>`**:
    - Limits execution to specific hosts or groups.
    - Useful for targeting specific subsets of hosts.
    - Example:
      ```
      ansible-playbook -l web_servers myplaybook.yml
      ```

17. **`--skip-tags`**:
    - Excludes specific tags during playbook execution.
    - Example:
      ```
      ansible-playbook --skip-tags packages myplay

18. **`-e` or `--extra-vars`**: This option allows you to pass extra variables to your playbook. You can use it to override variables defined in your playbook or provide additional input during execution. For example:
   ```
   ansible-playbook myplaybook.yml -e "my_variable=value"
   ```

19. **`-h` or `--help`**: Use this option to display the help message, which provides information about the available options and their usage. It's always a good idea to check the help documentation when you're unsure about a specific command. 😊

20. **`-t` or `--tags`**: Tags allow you to selectively run specific tasks or roles within your playbook. You can assign tags to tasks in your playbook, and then use this option to execute only the tagged tasks. For example:
   ```
   ansible-playbook myplaybook.yml -t my_tag
   ```



### Reference 
[Ansible Playbook Options - If you’d like more details or need additional options](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html#ansible-playbook)