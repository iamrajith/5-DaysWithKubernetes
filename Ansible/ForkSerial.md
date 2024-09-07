# Managing Playbook Execution with Fork, Serial, and Throttle in Ansible
## Ansible playbook execution focusing on the linear strategy

Let's see the default behavior of an Ansible playbook execution focusing on the linear strategy and the use of forks.

#### MyTestPlaybook.yaml
```yaml
- name: Controlling Playbook Execution with Fork, Serial, and Throttle
  hosts: all
  gather_facts: no
  tasks:
    - name: This is the first task
      debug:
        msg: Task 1 executed

    - name: This is the second task
      debug:
        msg: Task 2 executed

    - name: This is the third task
      debug:
        msg: Task 3 executed
```

By default, Ansible uses the **linear strategy**, which means tasks are executed sequentially on all hosts. Each task in the playbook must be completed on all hosts before the next task begins.

#### Forks
The **forks** parameter controls the number of parallel processes Ansible will use to execute tasks. By default, Ansible uses five forks, which can be adjusted in the "Ansible" configuration file (`ansible.cfg`) or via the command line.

### Example Execution
With the linear strategy and five forks, the playbook will execute as follows:
1. **Task 1** will run on up to 5 hosts in parallel.
2. Once **Task 1** completes on all hosts, **Task 2** will start.
3. **Task 2** will also run on up to 5 hosts in parallel.
4. Finally, **Task 3** will run on up to 5 hosts in parallel.

This setup ensures tasks are executed in the specified order, but multiple hosts can be processed simultaneously, improving efficiency.

Let us see how it works.

```bash
[rajith@controlnode ~]$ ansible-playbook -i inventory MyTestPlaybook.yaml -k
SSH password:

PLAY [Controlling Playbook Execution with Fork, Serial, and Throttle] ***********************************************************************************************************************

TASK [This is the first task] ***************************************************************************************************************************************************************
ok: [node1] => {
    "msg": "Task 1 executed"
}
ok: [node2] => {
    "msg": "Task 1 executed"
}
ok: [node3] => {
    "msg": "Task 1 executed"
}
ok: [node4] => {
    "msg": "Task 1 executed"
}
ok: [node5] => {
    "msg": "Task 1 executed"
}
ok: [node6] => {
    "msg": "Task 1 executed"
}

TASK [This is the second task] **************************************************************************************************************************************************************
ok: [node1] => {
    "msg": "Task 2 executed"
}
ok: [node2] => {
    "msg": "Task 2 executed"
}
ok: [node3] => {
    "msg": "Task 2 executed"
}
ok: [node4] => {
    "msg": "Task 2 executed"
}
ok: [node5] => {
    "msg": "Task 2 executed"
}
ok: [node6] => {
    "msg": "Task 2 executed"
}

TASK [This is the third task] ***************************************************************************************************************************************************************
ok: [node1] => {
    "msg": "Task 3 executed"
}
ok: [node2] => {
    "msg": "Task 3 executed"
}
ok: [node3] => {
    "msg": "Task 3 executed"
}
ok: [node4] => {
    "msg": "Task 3 executed"
}
ok: [node5] => {
    "msg": "Task 3 executed"
}
ok: [node6] => {
    "msg": "Task 3 executed"
}

PLAY RECAP **********************************************************************************************************************************************************************************
node1                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node2                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node3                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node4                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node5                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node6                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[rajith@controlnode ~]$
```
## Understanding the distinction between using 'serial' and linear execution in an Ansible playbook.

let's dive into this lab exercise and understand the difference between using `serial: 3` and a linear execution in an Ansible playbook.

### Lab Exercise Explanation

#### Playbook with `serial: 3`
Here's the playbook you provided:

```yaml
- name: Controlling Playbook Execution with Fork, Serial, and Throttle
  hosts: all
  gather_facts: no
  serial: 3
  tasks:
    - name: This is the first task
      debug:
        msg: Task 1 executed

    - name: This is the second task
      debug:
        msg: Task 2 executed

    - name: This is the third task
      debug:
        msg: Task 3 executed
```

#### Execution with `serial: 3`
When you specify `serial: 3`, Ansible will execute the playbook in batches of 3 hosts at a time. This means that if you have 9 hosts, the playbook will run on the first 3 hosts, then the next 3, and finally the last 3. This can be useful for rolling updates or when you want to limit the number of hosts being updated simultaneously to reduce the risk of widespread failure.

**Example Execution:**
1. First batch (3 hosts): Task 1, Task 2, Task 3
2. Second batch (next 3 hosts): Task 1, Task 2, Task 3
3. Third batch (last 3 hosts): Task 1, Task 2, Task 3

#### Linear Execution (without `serial`)
If you remove the `serial` directive, Ansible will attempt to execute the playbook on all hosts simultaneously (up to the limit set by the `forks` parameter, which defaults to 5).

**Example Execution:**
- All hosts (up to the limit of forks): Task 1, Task 2, Task 3

### Difference in Execution

1. **Concurrency:**
   - **With `serial: 3`:** The playbook runs in batches of 3 hosts, ensuring that only 3 hosts are being processed at any given time.
   - **Without `serial`:** The playbook runs on all hosts simultaneously, limited by the `forks` parameter.

2. **Control:**
   - **With `serial: 3`:** Provides more control over the deployment process, allowing for staged rollouts and reducing the risk of widespread issues.
   - **Without `serial`:** Faster execution but with less control, which might be riskier in large environments.

3. **Resource Utilization:**
   - **With `serial: 3`:** More predictable and controlled resource usage, as only a subset of hosts are being processed at a time.
   - **Without `serial`:** Higher resource usage as more hosts are processed simultaneously.

### Practical Example

Let's say you have 6 hosts: `host1`, `host2`, `host3`, `host4`, `host5`, and `host6`.

- **With `serial: 3`:**
  - First batch: `host1`, `host2`, `host3`
  - Second batch: `host4`, `host5`, `host6`

- **Without `serial`:**
  - All 6 hosts are processed simultaneously (up to the limit of `forks`).


```bash

[rajith@controlnode ~]$ ansible-playbook -i inventory MyTestPlaybook.yaml -k
SSH password:

PLAY [Controlling Playbook Execution with Fork, Serial, and Throttle] ***********************************************************************************************************************************************************************

TASK [This is the first task] ***************************************************************************************************************************************************************************************************************
ok: [node1] => {
    "msg": "Task 1 executed"
}
ok: [node2] => {
    "msg": "Task 1 executed"
}
ok: [node3] => {
    "msg": "Task 1 executed"
}

TASK [This is the second task] **************************************************************************************************************************************************************************************************************
ok: [node1] => {
    "msg": "Task 2 executed"
}
ok: [node2] => {
    "msg": "Task 2 executed"
}
ok: [node3] => {
    "msg": "Task 2 executed"
}

TASK [This is the third task] ***************************************************************************************************************************************************************************************************************
ok: [node1] => {
    "msg": "Task 3 executed"
}
ok: [node2] => {
    "msg": "Task 3 executed"
}
ok: [node3] => {
    "msg": "Task 3 executed"
}

PLAY [Controlling Playbook Execution with Fork, Serial, and Throttle] ***********************************************************************************************************************************************************************

TASK [This is the first task] ***************************************************************************************************************************************************************************************************************
ok: [node4] => {
    "msg": "Task 1 executed"
}
ok: [node5] => {
    "msg": "Task 1 executed"
}
ok: [node6] => {
    "msg": "Task 1 executed"
}

TASK [This is the second task] **************************************************************************************************************************************************************************************************************
ok: [node4] => {
    "msg": "Task 2 executed"
}
ok: [node5] => {
    "msg": "Task 2 executed"
}
ok: [node6] => {
    "msg": "Task 2 executed"
}

TASK [This is the third task] ***************************************************************************************************************************************************************************************************************
ok: [node4] => {
    "msg": "Task 3 executed"
}
ok: [node5] => {
    "msg": "Task 3 executed"
}
ok: [node6] => {
    "msg": "Task 3 executed"
}

PLAY RECAP **********************************************************************************************************************************************************************************************************************************
node1                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node2                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node3                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node4                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node5                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node6                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[rajith@controlnode ~]$

```

### Conclusion

Using `serial` in your playbook provides a way to control the number of hosts being processed at a time, which can be crucial for managing resources and reducing risks during deployments. It allows for more controlled and staged rollouts compared to linear execution, which processes all hosts simultaneously.


Let us see the example Fork and throttle in the next module.

ForkAndThrottle

Feel free to run the playbook with and without the `serial` directive to observe the differences in execution.