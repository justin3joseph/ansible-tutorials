---

# Lab 6 - Installing Multiple Packages Using Loops

One of the most common uses of loops is installing multiple software packages.

Without loops, you would write one task for each package.

Example:

```yaml
- name: Install nginx
  apt:
    name: nginx
    state: present

- name: Install git
  apt:
    name: git
    state: present

- name: Install curl
  apt:
    name: curl
    state: present
```

This becomes repetitive.

Instead, use a loop.

---

## Step 1 - Create a Playbook

```bash
nano package-loop.yml
```

Paste the following.

```yaml
---
- name: Install Multiple Packages

  hosts: servers

  become: yes

  tasks:

    - name: Install Packages

      apt:

        name: "{{ item }}"

        state: present

      loop:

        - git

        - curl

        - tree

        - vim
```

Save the file.

---

## Understanding the Playbook

`become: yes`

Runs the package installation with root privileges.

`item`

Contains one package name during each iteration.

Execution order:

```
Iteration 1

git

↓

Install git

-------------------

Iteration 2

curl

↓

Install curl

-------------------

Iteration 3

tree

↓

Install tree

-------------------

Iteration 4

vim

↓

Install vim
```

---

## Step 2 - Execute the Playbook

```bash
ansible-playbook -i inventory.ini package-loop.yml
```

---

## Expected Output

```
TASK [Install Packages]

changed: [client] => (item=git)

changed: [client] => (item=curl)

changed: [client] => (item=tree)

changed: [client] => (item=vim)
```

---

## Verify Installation

Login to the client node.

```bash
ssh student@client
```

Check each package.

```bash
git --version
```

```bash
curl --version
```

```bash
tree --version
```

```bash
vim --version
```

---

# Lab 7 - Using Variables for Package Lists

Instead of writing package names directly,

store them inside a variable.

```yaml
---
- name: Install Packages

  hosts: servers

  become: yes

  vars:

    packages:

      - git

      - curl

      - tree

      - vim

  tasks:

    - name: Install All Packages

      apt:

        name: "{{ item }}"

        state: present

      loop: "{{ packages }}"
```

---

## Why Use Variables?

Advantages:

- Easier maintenance
- Reusable package lists
- Cleaner playbooks
- Better readability

---

# Lab 8 - Creating Multiple Directories

Suppose every server requires these directories.

```
/opt/project

/opt/scripts

/opt/logs
```

Without loops,

three tasks are required.

With loops,

only one task is needed.

---

## Step 1

Create

```bash
nano directory-loop.yml
```

Paste

```yaml
---
- name: Create Directories

  hosts: servers

  become: yes

  tasks:

    - name: Create Project Directories

      file:

        path: "{{ item }}"

        state: directory

        mode: "0755"

      loop:

        - /opt/project

        - /opt/scripts

        - /opt/logs
```

---

## Run

```bash
ansible-playbook -i inventory.ini directory-loop.yml
```

---

## Expected Output

```
changed: [client] => (item=/opt/project)

changed: [client] => (item=/opt/scripts)

changed: [client] => (item=/opt/logs)
```

---

## Verify

Login to the client.

Run

```bash
ls -ld /opt/project
```

```bash
ls -ld /opt/scripts
```

```bash
ls -ld /opt/logs
```

---

# Lab 9 - Creating Multiple Empty Files

Create a playbook.

```bash
nano file-loop.yml
```

Paste

```yaml
---
- name: Create Files

  hosts: servers

  become: yes

  tasks:

    - name: Create Empty Files

      file:

        path: "{{ item }}"

        state: touch

      loop:

        - /tmp/file1.txt

        - /tmp/file2.txt

        - /tmp/file3.txt
```

---

## Run

```bash
ansible-playbook -i inventory.ini file-loop.yml
```

---

## Verify

```bash
ls -l /tmp/file1.txt
```

```bash
ls -l /tmp/file2.txt
```

```bash
ls -l /tmp/file3.txt
```

---

# Lab 10 - Creating Multiple Users

> **Note:** This lab creates Linux user accounts. Use it only on your lab VM or test environment.

Create a playbook.

```bash
nano user-loop.yml
```

Paste

```yaml
---
- name: Create Users

  hosts: servers

  become: yes

  tasks:

    - name: Create Users

      user:

        name: "{{ item }}"

        state: present

      loop:

        - devuser1

        - devuser2

        - devuser3
```

---

## Run

```bash
ansible-playbook -i inventory.ini user-loop.yml
```

---

## Verify

On the client node.

```bash
id devuser1
```

```bash
id devuser2
```

```bash
id devuser3
```

---

# Understanding Loop Execution

When creating users,

Ansible internally performs:

```
Create devuser1

↓

Create devuser2

↓

Create devuser3
```

Each execution is a separate iteration of the same task.

---

# Using Meaningful Variables

Instead of

```yaml
loop:

  - git

  - curl

  - tree
```

Prefer

```yaml
vars:

  packages:

    - git

    - curl

    - tree

loop: "{{ packages }}"
```

This makes your playbooks easier to update and reuse.

---

# Best Practices

✔ Store long lists in variables.

✔ Use loops instead of repeating tasks.

✔ Group related items together.

✔ Use descriptive variable names.

✔ Verify the results after execution.

---

# Common Mistakes

## Incorrect Indentation

Wrong

```yaml
loop:
- git
```

Correct

```yaml
loop:

  - git
```

---

## Forgetting Quotes Around Variables

Wrong

```yaml
name: {{ item }}
```

Correct

```yaml
name: "{{ item }}"
```

---

## Missing Privileges

Creating users or installing packages requires elevated privileges.

Always use

```yaml
become: yes
```

when required.

---

# Verification Checklist

Verify that you can:

- Install multiple packages using a loop.
- Store package names in a variable.
- Create multiple directories.
- Create multiple files.
- Create multiple Linux users.
- Verify the results on the managed node.

---

# Lab Exercise 1

Install the following packages using a loop.

```
wget

zip

unzip
```

Verify that each package is installed.

---

# Lab Exercise 2

Create the following directories.

```
/opt/apps

/opt/backup

/opt/config
```

Verify that all directories exist.

---

# Lab Exercise 3

Create the following empty files.

```
/tmp/app.log

/tmp/error.log

/tmp/access.log
```

Verify that the files were created.

---

## Continue to Part 2B

In Part 2B, you will learn:

- Registering Loop Results
- Combining Loops with `when`
- Understanding Registered Loop Output
- Troubleshooting
- Challenge Lab
- Viva Questions
- Summary
