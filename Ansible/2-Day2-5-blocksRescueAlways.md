## Grouping tasks with blocks

Ansible playbook that demonstrates the use of **blocks**, along with **rescue** and **always** sections.

```yaml
---
- name: Attempt and Graceful Rollback Demo
  hosts: all
  tasks:
    - name: Print a message
      ansible.builtin.debug:
        msg: 'I execute normally'
      # This task prints a message indicating normal execution.

    - name: Force a failure
      ansible.builtin.command: /bin/false
      # This task intentionally fails by executing a non-existent command.

    - name: Never print this
      ansible.builtin.debug:
        msg: 'I never execute, due to the above task failing, :-('
      # This task will not execute because the previous task failed.

  rescue:
    - name: Print when errors
      ansible.builtin.debug:
        msg: 'I caught an error'
      # In case of an error, this task prints a message.

    - name: Force a failure in middle of recovery! >:-)
      ansible.builtin.command: /bin/false
      # This task intentionally fails during the recovery phase.

    - name: Never print this
      ansible.builtin.debug:
        msg: 'I also never execute :-('
      # This task will not execute due to the previous task failing.

  always:
    - name: Always do this
      ansible.builtin.debug:
        msg: "This always executes"
      # Regardless of success or failure, this task always runs.
```

Explanation:
1. **Print a message**:
   - This task prints a message indicating normal execution.
   - It will execute unless there's a catastrophic failure before this task.

2. **Force a failure**:
   - This task intentionally fails by executing a non-existent command (`/bin/false`).
   - Since it fails, the subsequent task in the same block won't execute.

3. **Never print this**:
   - This task will not execute because the previous task failed.
   - It's part of the same block as the failed task.

4. **Rescue section**:
   - If any task in the block fails, the rescue section is triggered.
   - **Print when errors** task will execute and print a message.

5. **Force a failure in middle of recovery! >:-)**:
   - This task intentionally fails during the recovery phase.
   - It's part of the rescue section.

6. **Never print this** (rescue section):
   - This task will not execute due to the previous task failing.
   - It's also part of the rescue section.

7. **Always section**:
   - Regardless of success or failure, this section always runs.
   - The **Always do this** task prints a message.

This example is directly copy pasted from the [Ansible documentation.](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_blocks.html)