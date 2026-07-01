---

# Lab 6 - Using Registered Variables in Conditions

In Lab 08, we learned how to store the output of a command using the `register` keyword.

Now, let's use that output to decide whether a task should execute.

---

## Step 1 - Create a New Playbook

```bash
nano register-condition.yml
```

Paste the following:

```yaml
---
- name: Registered Variable Condition Demo

  hosts: servers

  tasks:

    - name: Get Current User

      command: whoami

      register: current_user

    - name: Display Welcome Message

      debug:

        msg: "Logged in as root"

      when: current_user.stdout == "root"
```

Save the file.

---

## Step 2 - Execute the Playbook

```bash
ansible-playbook -i inventory.ini register-condition.yml
```

---

## Expected Output

If the remote user is **root**

```
TASK [Display Welcome Message]

ok

msg:

Logged in as root
```

Otherwise

```
TASK [Display Welcome Message]

skipping
```

---

# Understanding the Playbook

The command

```bash
whoami
```

returns

```
ubuntu
```

or

```
root
```

The output is stored inside

```yaml
current_user.stdout
```

The condition checks the value.

If it matches,

the task executes.

---

# Lab 7 - Numeric Comparison

Conditions can compare numbers.

Example

```yaml
---
- name: Memory Check

  hosts: servers

  gather_facts: yes

  tasks:

    - name: High Memory Server

      debug:

        msg: "Server has more than 2 GB RAM"

      when: ansible_memtotal_mb > 2048
```

Run

```bash
ansible-playbook -i inventory.ini when.yml
```

---

## Expected Output

If memory is greater than 2 GB

```
Server has more than 2 GB RAM
```

Otherwise

```
skipping
```

---

# Numeric Operators

| Operator | Description |
|----------|-------------|
| `>` | Greater Than |
| `<` | Less Than |
| `>=` | Greater Than or Equal To |
| `<=` | Less Than or Equal To |

Example

```yaml
when: ansible_memtotal_mb >= 4096
```

---

# Lab 8 - Multiple Conditions using AND

Suppose we want to execute a task only if

- Operating System is Ubuntu
- RAM is greater than 2 GB

Playbook

```yaml
---
- name: Multiple Conditions

  hosts: servers

  gather_facts: yes

  tasks:

    - name: Ubuntu Server with Sufficient RAM

      debug:

        msg: "Ready for Production"

      when:

        - ansible_distribution == "Ubuntu"

        - ansible_memtotal_mb > 2048
```

---

## Understanding Multiple Conditions

When multiple conditions are written as a list,

Ansible treats them as **AND**.

Meaning

```
Condition 1

AND

Condition 2
```

Both must be true.

---

# Lab 9 - Using OR Operator

Sometimes

only one condition needs to be true.

Example

```yaml
---
- name: Ubuntu or Debian

  hosts: servers

  gather_facts: yes

  tasks:

    - debug:

        msg: "Debian Family Detected"

      when:

        ansible_distribution == "Ubuntu"

        or

        ansible_distribution == "Debian"
```

---

## Expected Output

Ubuntu

```
Debian Family Detected
```

Debian

```
Debian Family Detected
```

Rocky Linux

```
skipping
```

---

# Lab 10 - Using NOT Operator

Execute the task only if

the operating system is NOT Ubuntu.

```yaml
---
- name: NOT Operator

  hosts: servers

  gather_facts: yes

  tasks:

    - debug:

        msg: "This is NOT Ubuntu"

      when:

        ansible_distribution != "Ubuntu"
```

---

# Lab 11 - Using Multiple Registered Variables

Create

```yaml
---
- name: Multiple Registers

  hosts: servers

  tasks:

    - command: hostname

      register: host

    - command: whoami

      register: user

    - debug:

        msg:

          - "Hostname : {{ host.stdout }}"

          - "User : {{ user.stdout }}"
```

Run

```bash
ansible-playbook -i inventory.ini register-condition.yml
```

---

# Lab 12 - Installing Packages Conditionally

This is one of the most common real-world examples.

```yaml
---
- name: Install Web Server

  hosts: servers

  become: yes

  gather_facts: yes

  tasks:

    - name: Install Nginx on Ubuntu

      apt:

        name: nginx

        state: present

      when: ansible_distribution == "Ubuntu"

    - name: Install Apache on Rocky Linux

      dnf:

        name: httpd

        state: present

      when: ansible_distribution == "Rocky"
```

---

# Why is This Useful?

One playbook can manage

- Ubuntu
- Debian
- Rocky Linux
- Red Hat

without modification.

---

# Best Practices

✔ Use Facts instead of hardcoding values.

✔ Keep conditions simple.

✔ Use meaningful task names.

✔ Test conditions using the `debug` module before running destructive tasks.

✔ Use registered variables only when required.

---

# Common Mistakes

## Using a Variable Before Registering It

Wrong

```yaml
when: output.stdout == "root"
```

Correct

```yaml
register: output
```

before using it.

---

## Wrong Fact Name

Wrong

```yaml
ansible_os
```

Correct

```yaml
ansible_distribution
```

---

## Wrong Comparison

Wrong

```yaml
when: memory = 2048
```

Correct

```yaml
when: memory == 2048
```

---

# Troubleshooting

## Task Always Skipped

Check

```bash
ansible servers -i inventory.ini -m setup -a "filter=ansible_distribution"
```

Verify the actual value of the fact.

---

## Variable Undefined

Check

- Variable spelling
- Indentation
- Register task executed successfully

---

## Verify Registered Output

```yaml
- debug:

    var: current_user
```

This displays all fields.

---

# Lab Exercise 1

Display

```
High Memory Server
```

only if

```
ansible_memtotal_mb > 4096
```

---

# Lab Exercise 2

Execute

```bash
hostname
```

Store the output.

Display

```
Production Server
```

only if

```
hostname == "server01"
```

---

# Lab Exercise 3

Display

```
Ubuntu Production Server
```

only if

- Ubuntu
- Memory greater than 2 GB

---

# Challenge Lab

Create a playbook that performs the following.

1. Gather Facts.

2. Execute

```
hostname
```

3. Register the output.

4. Display

```
Hostname

Operating System

Kernel

Memory

IP Address

Current User
```

5. Display

```
Production Ready
```

only if

- Ubuntu
- RAM > 2048 MB

---

# Real-World Scenario

Suppose your organization has:

| Server | Operating System |
|---------|------------------|
| Web01 | Ubuntu |
| Web02 | Ubuntu |
| DB01 | Rocky Linux |
| App01 | Debian |

One Ansible playbook can:

- Install **nginx** on Ubuntu
- Install **httpd** on Rocky Linux
- Skip unsupported systems
- Display custom messages based on the operating system

This is why conditional statements are one of the most powerful features in Ansible.

---

# Verification Checklist

Verify that you can:

- Use `when`
- Compare strings
- Compare numbers
- Use Registered Variables
- Use Ansible Facts
- Use `AND`
- Use `OR`
- Use `NOT`
- Execute tasks conditionally

---

# Viva Questions

### 1. What is the purpose of the `when` keyword?

---

### 2. Which comparison operator checks equality?

---

### 3. Can `when` use Registered Variables?

---

### 4. Can `when` use Ansible Facts?

---

### 5. What does `!=` mean?

---

### 6. How do you write multiple AND conditions?

---

### 7. How do you use the OR operator?

---

### 8. Why are Conditional Statements important?

---

### 9. Which Ansible Fact identifies the operating system?

---

### 10. Can one playbook support multiple Linux distributions?

---

# Summary

Congratulations!

In this lab, you learned:

- How Conditional Statements work.
- Using the `when` keyword.
- Comparing variables.
- Comparing Ansible Facts.
- Using Registered Variables.
- Numeric comparisons.
- Logical operators (`AND`, `OR`, `NOT`).
- Real-world conditional playbooks.
- Best practices and troubleshooting.

Conditional execution allows Ansible to make intelligent decisions, enabling a single playbook to automate tasks across different operating systems and environments.

---

# Next Lab

➡ **Lab 10 – Loops in Ansible**

In the next lab, you will learn:

- Why loops are used.
- The `loop` keyword.
- Replacing repetitive tasks.
- Looping through packages, users, and files.
- Registering loop results.
- Practical automation examples.
