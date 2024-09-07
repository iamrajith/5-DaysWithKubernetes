

Let’s break down the playbook execution step-by-step in simple terms.

Playbook Execution

```yaml
[rajith@controlnode ~]$ cat MyTestPlaybook.yaml
- name : Controlling Playbook Execution with Fork, Serial, and Throttle
  hosts: all
  gather_facts: no
  tasks :

  - name : This is the first task [ keeping the task to sleep in each server for 2 mints ]
    shell: date ; sleep 2m
    register: system_time
    throttle: 1
    delegate_to: localhost



  - name : This is the second task [ Displaying the time of task execution on each server ]
    debug :
      msg: "Task 1 executed , the system time is {{ system_time.stdout }} "
[rajith@controlnode ~]$
```