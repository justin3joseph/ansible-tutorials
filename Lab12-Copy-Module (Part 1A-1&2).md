# Lab 12 - Copy Module (Part 1A-1)

> **Course:** Ansible for Beginners
>
> **Lab Duration:** 90 Minutes
>
> **Difficulty:** ⭐⭐ Beginner

---

# Lab Objectives

After completing this lab, you will be able to:

- Understand the purpose of the Copy Module.
- Differentiate between the File Module and the Copy Module.
- Copy files from the Ansible Control Node to Managed Nodes.
- Understand the `src` and `dest` parameters.
- Verify copied files on the managed node.

---

# Prerequisites

Before starting this lab, complete the following:

- Lab 01 – Environment Setup
- Lab 02 – Inventory
- Lab 03 – Ad-hoc Commands
- Lab 04 – First Playbook
- Lab 05 – Variables
- Lab 06 – Variable Files
- Lab 07 – Ansible Facts
- Lab 08 – Registered Variables
- Lab 09 – Conditional Statements
- Lab 10 – Loops
- Lab 11 – File Module

---

# Lab Architecture

```mermaid
graph LR

A[Control Node]

A -->|Copy Module| B[Managed Node]

B --> C[/tmp]

C --> D[welcome.txt]
```

---

# What is the Copy Module?

The **Copy Module** is used to copy files from the **Ansible Control Node** to one or more **Managed Nodes**.

Unlike the **File Module**, which creates or manages files and directories, the Copy Module transfers the actual file and its contents.

---

# Why Do We Use the Copy Module?

System administrators often need to distribute files such as:

- Configuration files
- HTML pages
- Shell scripts
- Log templates
- Application properties
- License files

Instead of manually copying these files to each server, Ansible automates the process.

---

# File Module vs Copy Module

| File Module | Copy Module |
|-------------|-------------|
| Creates empty files | Copies files with content |
| Creates directories | Copies configuration files |
| Changes permissions | Preserves or sets permissions |
| Creates links | Deploys application files |

---

# Syntax

```yaml
- name: Copy File

  copy:

    src: source_file

    dest: destination_path
```

---

# Understanding Parameters

### src

The location of the file on the **Ansible Control Node**.

Example

```yaml
src: files/welcome.txt
```

---

### dest

The destination path on the **Managed Node**.

Example

```yaml
dest: /tmp/welcome.txt
```

---

# Lab Directory Structure

Create a dedicated directory for this lab.

```bash
cd ~/ansible-labs

mkdir -p files
```

Verify.

```bash
ls
```

Expected

```
files
inventory.ini
```

---

# Lab 1 - Create the Source File

Navigate to the files directory.

```bash
cd ~/ansible-labs/files
```

Create a file.

```bash
nano welcome.txt
```

Add the following content.

```
Welcome to the Ansible Copy Module Lab.

This file was created on the Control Node.
```

Save the file.

---

# Verify the File

```bash
cat welcome.txt
```

Expected Output

```
Welcome to the Ansible Copy Module Lab.

This file was created on the Control Node.
```

---

# Lab 2 - Copy the File to the Managed Node

Return to the Ansible working directory.

```bash
cd ~/ansible-labs
```

Create a new playbook.

```bash
nano copy-file.yml
```

Paste the following.

```yaml
---
- name: Copy a File to Managed Node

  hosts: servers

  become: yes

  tasks:

    - name: Copy welcome.txt

      copy:

        src: files/welcome.txt

        dest: /tmp/welcome.txt
```

Save the playbook.

---

# Understanding the Playbook

The `copy` module reads the file from:

```
~/ansible-labs/files/welcome.txt
```

and copies it to:

```
/tmp/welcome.txt
```

on the managed node.

---

# Step 2 - Execute the Playbook

```bash
ansible-playbook -i inventory.ini copy-file.yml
```

---

# Expected Output

```
PLAY [Copy a File to Managed Node]

TASK [Copy welcome.txt]

changed: [client]

PLAY RECAP

client : ok=1 changed=1 unreachable=0 failed=0
```

---

# Verify on the Managed Node

Login to the client.

```bash
ssh student@client
```

Check whether the file exists.

```bash
ls -l /tmp/welcome.txt
```

Expected

```
-rw-r--r-- 1 root root ...
```

Display the contents.

```bash
cat /tmp/welcome.txt
```

Expected

```
Welcome to the Ansible Copy Module Lab.

This file was created on the Control Node.
```

---

# Understanding Idempotency

Run the playbook again.

```bash
ansible-playbook -i inventory.ini copy-file.yml
```

Expected

```
ok: [client]
```

Since the file has not changed, Ansible does not copy it again.

If you edit the source file and rerun the playbook, Ansible detects the change and updates the destination file.

---

# Lab 3 - Updating the Source File

Edit the source file.

```bash
nano files/welcome.txt
```

Append the following line.

```
Ansible automatically detects file changes.
```

Save the file.

---

# Execute Again

```bash
ansible-playbook -i inventory.ini copy-file.yml
```

Expected

```
changed: [client]
```

---

# Verify

```bash
cat /tmp/welcome.txt
```

Expected

```
Welcome to the Ansible Copy Module Lab.

This file was created on the Control Node.

Ansible automatically detects file changes.
```

---

# How Does Ansible Know the File Changed?

The Copy Module compares the source file with the destination file.

If the content is different,

Ansible copies the updated file.

Otherwise,

the task is skipped.

This behavior makes the Copy Module **idempotent**.

---

# Verification Checklist

Verify that you can:

- Create a source file.
- Copy the file to the managed node.
- Verify the copied file.
- Modify the source file.
- Observe Ansible updating only changed files.

---

# Lab Exercise 1

Create a file named

```
message.txt
```

inside the `files` directory.

Add the following content.

```
Hello from the Ansible Control Node.
```

Copy it to

```
/tmp/message.txt
```

Verify the file on the managed node.

---

# Lab Exercise 2

Modify `message.txt`.

Run the playbook again.

Observe whether Ansible reports

```
changed
```

or

```
ok
```

Explain why.

---

# Summary

Congratulations!

In this lab, you learned:

- What the Copy Module is.
- The difference between the File Module and the Copy Module.
- How to copy a file from the Control Node to a Managed Node.
- How `src` and `dest` work.
- How Ansible detects file changes using idempotency.

---

# Next (Part 1A-2)

In the next section, you will learn:

- Copying files to different directories
- Setting file permissions during copy
- Setting owner and group
- Creating destination directories
- Common mistakes
- Mini challenge

---

# Lab 4 - Copying a File to Another Directory

In the previous lab, we copied a file to:

```
/tmp
```

Now let's copy the same file to a different location.

---

## Step 1 - Create the Destination Directory

Create a new playbook.

```bash
nano create-directory.yml
```

Paste the following.

```yaml
---
- name: Create Application Directory

  hosts: servers

  become: yes

  tasks:

    - name: Create Directory

      file:

        path: /opt/application

        state: directory

        mode: "0755"
```

Execute.

```bash
ansible-playbook -i inventory.ini create-directory.yml
```

---

## Step 2 - Copy the File

Create another playbook.

```bash
nano copy-application.yml
```

Paste.

```yaml
---
- name: Copy Application File

  hosts: servers

  become: yes

  tasks:

    - name: Copy Welcome File

      copy:

        src: files/welcome.txt

        dest: /opt/application/welcome.txt
```

---

## Execute

```bash
ansible-playbook -i inventory.ini copy-application.yml
```

---

## Verify

Login to the managed node.

```bash
ls -l /opt/application
```

Expected

```
welcome.txt
```

Display the contents.

```bash
cat /opt/application/welcome.txt
```

---

# Lab 5 - Setting File Permissions While Copying

Instead of copying the file first and changing permissions later,

the Copy Module allows us to do both in one task.

Modify the playbook.

```yaml
---
- name: Copy File with Permissions

  hosts: servers

  become: yes

  tasks:

    - name: Copy Welcome File

      copy:

        src: files/welcome.txt

        dest: /opt/application/welcome.txt

        mode: "0644"
```

---

## Execute

```bash
ansible-playbook -i inventory.ini copy-application.yml
```

---

## Verify

```bash
ls -l /opt/application/welcome.txt
```

Expected

```
-rw-r--r--
```

---

# Understanding the `mode` Parameter

```yaml
mode: "0644"
```

Meaning

| User | Permission |
|------|------------|
| Owner | Read + Write |
| Group | Read |
| Others | Read |

---

# Lab 6 - Setting Owner While Copying

If the user already exists on the managed node, we can assign ownership while copying.

Example

```yaml
---
- name: Copy File with Owner

  hosts: servers

  become: yes

  tasks:

    - name: Copy Welcome File

      copy:

        src: files/welcome.txt

        dest: /opt/application/welcome.txt

        owner: devuser1

        mode: "0644"
```

> **Note:** Replace `devuser1` with an existing user on your managed node if necessary.

---

## Verify

```bash
ls -l /opt/application/welcome.txt
```

Expected

```
devuser1
```

appears as the owner.

---

# Lab 7 - Setting Group While Copying

```yaml
---
- name: Copy File with Group

  hosts: servers

  become: yes

  tasks:

    - name: Copy Welcome File

      copy:

        src: files/welcome.txt

        dest: /opt/application/welcome.txt

        owner: devuser1

        group: devuser1

        mode: "0644"
```

---

## Verify

```bash
ls -l /opt/application/welcome.txt
```

Observe the group name.

---

# Lab 8 - Setting Owner, Group and Permissions Together

This is the recommended approach.

```yaml
---
- name: Deploy Application File

  hosts: servers

  become: yes

  tasks:

    - name: Deploy Welcome File

      copy:

        src: files/welcome.txt

        dest: /opt/application/welcome.txt

        owner: devuser1

        group: devuser1

        mode: "0644"
```

One task performs all required operations.

---

# Understanding `src` and `dest`

```
Control Node

~/ansible-labs/files/welcome.txt

↓

Copy Module

↓

Managed Node

/opt/application/welcome.txt
```

Remember:

- `src` is **always on the Control Node** (unless `remote_src: true` is used, which will be covered later).
- `dest` is **always on the Managed Node**.

---

# Common Mistakes

## Mistake 1 - Incorrect Source Path

Wrong

```yaml
src: welcome.txt
```

Correct

```yaml
src: files/welcome.txt
```

---

## Mistake 2 - Destination Directory Does Not Exist

If `/opt/application` does not exist,

the Copy Module cannot create the directory automatically.

Create it first using the File Module.

---

## Mistake 3 - Missing Root Privileges

Copying files into system directories usually requires:

```yaml
become: yes
```

---

## Mistake 4 - Invalid User

Always verify the user.

```bash
id devuser1
```

If the user does not exist,

the playbook fails.

---

# Best Practices

✔ Keep source files inside a dedicated `files/` directory.

✔ Use meaningful filenames.

✔ Set permissions during the copy operation.

✔ Set owner and group when deploying configuration files.

✔ Verify the copied file after execution.

---

# Verification Checklist

Verify that you can:

- Copy files into different directories.
- Set permissions during copy.
- Set owner during copy.
- Set group during copy.
- Verify copied files using Linux commands.

---

# Lab Exercise 3

Create a source file.

```
files/config.ini
```

Add the following content.

```
application_name=Inventory

version=1.0
```

Copy it to

```
/opt/application/config.ini
```

Set:

- Permission: `0644`

Verify the file.

---

# Lab Exercise 4

Create a file.

```
files/readme.txt
```

Copy it to

```
/tmp/readme.txt
```

Verify:

- File exists
- Content matches
- Permissions are correct

---

# Mini Challenge

Create a playbook that performs the following:

1. Creates the directory:

```
/opt/company
```

2. Copies:

```
welcome.txt

config.ini

readme.txt
```

into:

```
/opt/company
```

3. Set:

- Owner: `root` (or an appropriate existing user)
- Group: `root` (or an appropriate existing group)
- Permission: `0644`

4. Verify all files.

---

# Summary

Congratulations!

In this section, you learned how to:

- Copy files to different locations.
- Set permissions while copying.
- Set file ownership and group.
- Understand the `src` and `dest` parameters.
- Prepare destination directories before deployment.
- Follow best practices for file deployment.

---

# Next (Part 2)

In the next part of Lab 12, you will learn:

- Copying multiple files using loops
- Using variables with the Copy Module
- `backup: yes`
- `force: no`
- `remote_src: true`
- Best practices
- Troubleshooting
- Challenge lab
- Viva questions
