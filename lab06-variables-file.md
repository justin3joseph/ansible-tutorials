# Lab 06 - Using Variable Files in Ansible

> **Course:** Ansible for Beginners
>
> **Lab Duration:** 60–90 Minutes
>
> **Difficulty:** ⭐ Beginner

---

# Lab Objectives

By the end of this lab, you will be able to:

- Understand why variable files are used.
- Create a separate YAML file to store variables.
- Load variables using `vars_files`.
- Organize playbooks for better readability.
- Understand the difference between `vars` and `vars_files`.

---

# Prerequisites

Before starting this lab, ensure you have completed:

- Lab 01 - Environment Setup
- Lab 02 - Inventory
- Lab 03 - Ad-hoc Commands
- Lab 04 - First Playbook
- Lab 05 - Variables

---

# Lab Architecture

```mermaid
graph LR

A[variables.yml] --> B[Playbook]

B --> C[Master Node]

C -->|SSH| D[Client Node]
```

---

# Why Variable Files?

In Lab 05, we stored variables inside the playbook.

Example:

```yaml
vars:
  package_name: nginx
  service_name: nginx
```

This works well for small playbooks.

But imagine a project with:

- 50 variables
- 100 tasks
- Multiple environments

The playbook becomes difficult to read.

Instead, we separate variables into another file.

```
Playbook
     |
     |
Loads Variables
     |
     |
variables.yml
```

This makes playbooks:

- Cleaner
- Easier to maintain
- Easier to reuse

---

# Directory Structure

Inside your working directory:

```
ansible-labs/

│── inventory.ini
│── variable-file.yml
│
└── vars
      │
      └── variables.yml
```

---

# Lab 1 - Create a Variables Directory

Move into your Ansible working directory.

```bash
cd ~/ansible-labs
```

Create a new directory.

```bash
mkdir vars
```

Verify

```bash
ls
```

Expected Output

```
inventory.ini
first.yml
variables.yml
vars
```

---

# Lab 2 - Create a Variable File

Move inside the directory.

```bash
cd vars
```

Create a file.

```bash
nano variables.yml
```

Paste the following.

```yaml
trainer: Justin

course: DevOps

package_name: nginx

service_name: nginx
```

Save.

```
CTRL + O

Enter

CTRL + X
```

---

# Understanding the Variable File

Unlike a playbook,

this file contains only variables.

There are:

- No hosts
- No tasks
- No modules

It simply stores data.

---

# Lab 3 - Create the Playbook

Go back.

```bash
cd ..
```

Create a new playbook.

```bash
nano variable-file.yml
```

Paste the following.

```yaml
---
- name: Variable File Demo

  hosts: servers

  vars_files:

    - vars/variables.yml

  tasks:

    - name: Display Trainer

      debug:

        msg: "{{ trainer }}"

    - name: Display Course

      debug:

        msg: "{{ course }}"

    - name: Display Package

      debug:

        msg: "{{ package_name }}"
```

Save the file.

---

# Understanding the Playbook

## vars_files

```yaml
vars_files:

  - vars/variables.yml
```

This tells Ansible:

"Load all variables from this file."

Now every variable inside

```
variables.yml
```

can be used inside the playbook.

---

# Step 4 - Run the Playbook

```bash
ansible-playbook -i inventory.ini variable-file.yml
```

---

# Expected Output

```
PLAY [Variable File Demo]

TASK [Gathering Facts]

ok

TASK [Display Trainer]

ok:

msg: Justin

TASK [Display Course]

ok:

msg: DevOps

TASK [Display Package]

ok:

msg: nginx

PLAY RECAP

client

ok=4
```

---

# What Happened?

Ansible performed the following steps:

1. Read the playbook.
2. Found `vars_files`.
3. Opened `vars/variables.yml`.
4. Loaded all variables.
5. Executed each task.
6. Printed the variable values.

---

# Lab 4 - Modify the Variable File

Open the variable file.

```bash
nano vars/variables.yml
```

Change

```yaml
package_name: nginx
```

to

```yaml
package_name: apache2
```

Save.

Run again.

```bash
ansible-playbook -i inventory.ini variable-file.yml
```

---

# Expected Output

```
apache2
```

Notice:

The playbook was not modified.

Only the variable file changed.

---

# Lab 5 - Add More Variables

Modify

```yaml
trainer: Justin

course: DevOps

package_name: nginx

service_name: nginx

environment: Production

company: ABC Technologies
```

---

Modify the playbook.

```yaml
---
- name: Variable File Demo

  hosts: servers

  vars_files:

    - vars/variables.yml

  tasks:

    - debug:

        msg:

          - "Trainer : {{ trainer }}"

          - "Course : {{ course }}"

          - "Company : {{ company }}"

          - "Environment : {{ environment }}"

          - "Package : {{ package_name }}"

          - "Service : {{ service_name }}"
```

Run

```bash
ansible-playbook -i inventory.ini variable-file.yml
```

---

# Expected Output

```
Trainer : Justin

Course : DevOps

Company : ABC Technologies

Environment : Production

Package : nginx

Service : nginx
```

---

# Difference Between vars and vars_files

## vars

```yaml
vars:

  package_name: nginx
```

Variables are stored inside the playbook.

Suitable for:

- Small playbooks
- Temporary variables

---

## vars_files

```yaml
vars_files:

  - vars/variables.yml
```

Variables are stored separately.

Suitable for:

- Large projects
- Production environments
- Reusable configurations

---

# Advantages of Variable Files

✔ Cleaner playbooks

✔ Easier maintenance

✔ Reusable variables

✔ Better organization

✔ Easy environment management

---

# Common Mistakes

## Wrong File Path

```yaml
vars_files:

- variable.yml
```

If the file is actually inside

```
vars/
```

Ansible reports

```
File not found
```

---

## Incorrect Indentation

Wrong

```yaml
vars_files:

- vars/variables.yml
```

Correct

```yaml
vars_files:

  - vars/variables.yml
```

---

## Incorrect Extension

Wrong

```
variables.txt
```

Correct

```
variables.yml
```

---

# Verification

Check your variables.

```bash
cat vars/variables.yml
```

Check your playbook.

```bash
cat variable-file.yml
```

Run again.

```bash
ansible-playbook -i inventory.ini variable-file.yml
```

Everything should execute successfully.

---

# Lab Exercise 1

Create a variable file containing:

```
student_name

college

branch

semester
```

Display all variables.

---

# Lab Exercise 2

Create another variable file named

```
database.yml
```

Store

```
db_name

db_user

db_port

db_password
```

Display all values.

---

# Challenge Lab

Create the following directory structure.

```
ansible-labs/

vars/

application.yml

database.yml

users.yml
```

Store related variables in each file.

Create a playbook that loads all three variable files and prints every variable.

---

# Best Practices

✔ Keep variables in separate files.

✔ Use meaningful variable names.

✔ Organize variables by purpose.

✔ Store environment-specific variables separately.

Example

```
vars/

production.yml

testing.yml

development.yml
```

---

# Viva Questions

### 1. Why do we use variable files?

---

### 2. Which keyword loads external variable files?

---

### 3. Where should variable files be stored?

---

### 4. What is the difference between `vars` and `vars_files`?

---

### 5. Can a playbook load multiple variable files?

---

### 6. What happens if a variable file does not exist?

---

### 7. Which file extension is commonly used for variable files?

---

### 8. Why are variable files preferred in large projects?

---

# Summary

In this lab, you learned:

- Why variable files are useful.
- Creating a `vars` directory.
- Creating a YAML variable file.
- Loading variables using `vars_files`.
- Using variables stored outside the playbook.
- Organizing Ansible projects using separate configuration files.

---

# Next Lab

➡ **Lab 07 – Ansible Facts**

In the next lab, you will learn how Ansible automatically collects system information (Facts) such as:

- Hostname
- IP Address
- Operating System
- Kernel Version
- Memory
- CPU
- Network Interfaces

and use those facts inside playbooks.
