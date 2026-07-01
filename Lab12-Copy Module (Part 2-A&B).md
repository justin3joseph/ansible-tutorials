---

# Lab 9 - Copying Multiple Files Using Loops

In the previous labs, we copied one file at a time.

When deploying applications, we often need to copy several files.

Instead of writing multiple tasks, we can combine the **Copy Module** with **loops**.

---

## Step 1 - Prepare Source Files

On the **Control Node**, create additional files.

```bash
cd ~/ansible-labs/files
```

Create three files.

```bash
echo "Application Configuration" > config.ini

echo "This is the README file." > readme.txt

echo "Welcome to the Inventory Application." > index.html
```

Verify.

```bash
ls -l
```

Expected Output

```
config.ini

index.html

readme.txt

welcome.txt
```

---

# Lab 10 - Copy Multiple Files

Create a playbook.

```bash
cd ~/ansible-labs

nano copy-multiple.yml
```

Paste the following.

```yaml
---
- name: Copy Multiple Files

  hosts: servers

  become: yes

  tasks:

    - name: Create Destination Directory

      file:

        path: /opt/application

        state: directory

        mode: "0755"

    - name: Copy Files

      copy:

        src: "files/{{ item }}"

        dest: "/opt/application/{{ item }}"

        mode: "0644"

      loop:

        - welcome.txt

        - config.ini

        - readme.txt

        - index.html
```

---

## Execute

```bash
ansible-playbook -i inventory.ini copy-multiple.yml
```

---

## Verify

```bash
ls -l /opt/application
```

Expected

```
config.ini

index.html

readme.txt

welcome.txt
```

---

# Understanding the Loop

The variable

```yaml
{{ item }}
```

takes one filename at a time.

Iteration 1

```
welcome.txt
```

Iteration 2

```
config.ini
```

Iteration 3

```
readme.txt
```

Iteration 4

```
index.html
```

---

# Lab 11 - Using Variables with the Copy Module

Instead of writing the list directly inside the loop,

store it in a variable.

```yaml
---
- name: Copy Files Using Variables

  hosts: servers

  become: yes

  vars:

    application_files:

      - welcome.txt

      - config.ini

      - readme.txt

      - index.html

  tasks:

    - name: Copy Application Files

      copy:

        src: "files/{{ item }}"

        dest: "/opt/application/{{ item }}"

      loop: "{{ application_files }}"
```

---

## Why Use Variables?

Advantages

- Easier to update
- Cleaner playbooks
- Reusable
- Better readability

---

# Lab 12 - Using `backup: yes`

Sometimes a destination file already exists.

You may want to keep a backup before replacing it.

Example

```yaml
---
- name: Copy with Backup

  hosts: servers

  become: yes

  tasks:

    - name: Copy Configuration File

      copy:

        src: files/config.ini

        dest: /opt/application/config.ini

        backup: yes
```

---

## Execute

```bash
ansible-playbook -i inventory.ini copy-multiple.yml
```

---

## Verify

```bash
ls -l /opt/application
```

You may see a file similar to:

```
config.ini.12345.2026-07-01@15:20~
```

This is the backup created by Ansible.

---

# When Should We Use Backups?

Backups are useful when deploying:

- Application configuration
- Web server configuration
- Database configuration
- Production systems

This allows easy rollback if something goes wrong.

---

# Lab 13 - Using `force: no`

By default,

Ansible replaces the destination file if the content changes.

Sometimes,

you do **not** want to overwrite an existing file.

Example

```yaml
---
- name: Do Not Overwrite Existing File

  hosts: servers

  become: yes

  tasks:

    - name: Copy Welcome File

      copy:

        src: files/welcome.txt

        dest: /opt/application/welcome.txt

        force: no
```

---

## What Does `force: no` Do?

If

```
welcome.txt
```

already exists,

Ansible leaves it unchanged.

It only copies the file if it does not already exist.

---

## Verify

Run the playbook twice.

Notice that the second execution skips replacing the file.

---

# Lab 14 - Using `remote_src: true`

Normally,

the Copy Module copies files from the **Control Node**.

Sometimes,

the source file already exists on the **Managed Node**.

Example

```yaml
---
- name: Copy Existing Remote File

  hosts: servers

  become: yes

  tasks:

    - name: Copy Local File on Managed Node

      copy:

        src: /tmp/application.log

        dest: /tmp/application_backup.log

        remote_src: true
```

---

## What Does `remote_src: true` Mean?

Without

```yaml
remote_src: true
```

Ansible looks for the source file on the **Control Node**.

With

```yaml
remote_src: true
```

Ansible looks for the source file on the **Managed Node**.

---

# Architecture

Without `remote_src`

```
Control Node

↓

Managed Node
```

With `remote_src`

```
Managed Node

↓

Managed Node
```

---

## Execute

```bash
ansible-playbook -i inventory.ini remote-copy.yml
```

---

## Verify

```bash
ls -l /tmp/application_backup.log
```

---

# Summary of Copy Module Parameters

| Parameter | Purpose |
|-----------|---------|
| src | Source file |
| dest | Destination path |
| owner | File owner |
| group | File group |
| mode | File permissions |
| backup | Create backup before overwrite |
| force | Prevent overwrite if set to `no` |
| remote_src | Source file is on the managed node |

---

# Verification Checklist

Verify that you can:

- Copy multiple files using loops.
- Store filenames in variables.
- Create backups before overwriting.
- Prevent overwriting using `force: no`.
- Copy files already present on the managed node using `remote_src: true`.

---

# Continue to Part 2B

In the final part, you will learn:

- Best Practices
- Common Mistakes
- Troubleshooting
- Lab Exercises
- Challenge Lab
- Viva Questions
- Summary

---

# Best Practices

Following best practices helps you write playbooks that are secure, reusable, and easy to maintain.

---

## 1. Store Source Files in the `files/` Directory

A common Ansible project structure is:

```
ansible-labs/

├── inventory.ini

├── playbook.yml

└── files/

    ├── welcome.txt

    ├── config.ini

    ├── index.html
```

Keeping all source files in one place makes projects easier to manage.

---

## 2. Use Meaningful File Names

Instead of

```
file1.txt

file2.txt
```

use

```
nginx.conf

database.properties

application.yml
```

Meaningful names improve readability.

---

## 3. Set Permissions During Copy

Instead of changing permissions later,

set them during the copy operation.

Example

```yaml
copy:

  src: files/config.ini

  dest: /opt/application/config.ini

  mode: "0644"
```

---

## 4. Use Variables for Multiple Files

Instead of

```yaml
loop:

  - config.ini

  - index.html

  - readme.txt
```

store them in variables.

```yaml
vars:

  application_files:

    - config.ini

    - index.html

    - readme.txt
```

This makes future updates easier.

---

## 5. Backup Important Files

Before replacing configuration files,

enable backups.

```yaml
backup: yes
```

This is especially important on production servers.

---

## 6. Verify After Copying

Always confirm that the file was copied correctly.

Useful commands

```bash
ls -l
```

```bash
cat filename
```

```bash
stat filename
```

---

# Common Mistakes

---

## Mistake 1 - Wrong Source Location

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

The Copy Module creates files,

not directories.

Always create directories first.

Example

```yaml
file:

  path: /opt/application

  state: directory
```

---

## Mistake 3 - Missing Root Privileges

Copying files into

```
/etc

/opt

/usr
```

usually requires

```yaml
become: yes
```

---

## Mistake 4 - Incorrect File Permissions

Avoid

```yaml
mode: "0777"
```

Use the least permission required.

Example

```
0644
```

---

## Mistake 5 - Forgetting `remote_src`

If the source file already exists on the managed node,

remember to use

```yaml
remote_src: true
```

Otherwise,

Ansible searches for the file on the Control Node.

---

# Troubleshooting

---

## Error

```
Source file not found
```

Verify

```bash
ls files/
```

Ensure the source file exists.

---

## Error

```
Destination directory does not exist
```

Create it first.

```yaml
file:

  path: /opt/application

  state: directory
```

---

## Error

```
Permission denied
```

Verify

```yaml
become: yes
```

---

## Error

```
User does not exist
```

Check

```bash
id username
```

before assigning owner.

---

## Error

```
File not updated
```

Possible causes

- `force: no`
- File contents are identical
- Wrong destination path

---

# Verification Checklist

Before completing this lab,

verify that you can:

- Copy one file.
- Copy multiple files.
- Use loops.
- Use variables.
- Set permissions.
- Set owner.
- Set group.
- Create backups.
- Prevent overwriting.
- Use `remote_src`.

---

# Lab Exercise 1

Create

```
files/info.txt
```

Copy it to

```
/tmp/info.txt
```

Set

```
0644
```

Verify.

---

# Lab Exercise 2

Create

```
files/app.conf

files/db.conf

files/web.conf
```

Copy them to

```
/etc/demo/
```

using a loop.

---

# Lab Exercise 3

Modify

```
files/app.conf
```

Enable

```yaml
backup: yes
```

Run the playbook.

Verify that a backup file has been created.

---

# Lab Exercise 4

Create

```
/tmp/local.log
```

on the managed node.

Copy it to

```
/tmp/local_backup.log
```

using

```yaml
remote_src: true
```

---

# Mini Project

## Scenario

You are deploying a new web application.

The server requires the following structure.

```
/opt/webapp

├── index.html

├── app.conf

├── readme.txt

└── license.txt
```

---

## Requirements

Create one playbook that:

### Step 1

Creates

```
/opt/webapp
```

---

### Step 2

Copies all files from

```
files/
```

using a loop.

---

### Step 3

Assign

```
Owner

root

Group

root

Permission

0644
```

---

### Step 4

Enable

```yaml
backup: yes
```

---

### Step 5

Display

```
=====================================

WEB APPLICATION DEPLOYMENT COMPLETED

Files Copied Successfully

Permissions Applied

Backup Enabled

=====================================
```

using the `debug` module.

---

# Real-World Example

Suppose your company has **100 Linux servers**.

Every server requires:

```
nginx.conf

index.html

motd

application.properties
```

Instead of manually logging into each server,

Ansible can deploy all required files with one playbook.

This is one of the most common real-world uses of the Copy Module.

---

# Viva Questions

### 1. What is the purpose of the Copy Module?

---

### 2. What is the difference between the File Module and the Copy Module?

---

### 3. What does the `src` parameter represent?

---

### 4. What does the `dest` parameter represent?

---

### 5. What is the purpose of `backup: yes`?

---

### 6. What is the purpose of `force: no`?

---

### 7. When should `remote_src: true` be used?

---

### 8. Why should loops be used with the Copy Module?

---

### 9. Why should source files be stored in the `files/` directory?

---

### 10. How can you verify that a copied file is correct?

---

# Summary

Congratulations!

In this lab, you learned how to use the **Ansible Copy Module** to deploy files from the Control Node to Managed Nodes.

You learned to:

- Copy individual files.
- Copy multiple files using loops.
- Use variables to manage file lists.
- Set ownership, group, and permissions while copying.
- Create backups of existing files.
- Prevent overwriting with `force: no`.
- Copy files already present on the managed node using `remote_src: true`.
- Apply best practices and troubleshoot common issues.

The **Copy Module** is one of the most frequently used modules in Ansible because nearly every deployment requires configuration files, web pages, scripts, or application resources to be distributed across multiple servers.

---

# Key Takeaways

✅ `src` is on the **Control Node** by default.

✅ `dest` is always on the **Managed Node**.

✅ Use `backup: yes` for important configuration files.

✅ Use `force: no` when you want to preserve existing files.

✅ Use `remote_src: true` when copying files that already exist on the managed node.

✅ Combine loops with the Copy Module to deploy many files efficiently.

---

# Next Lab

➡ **Lab 13 – Template Module (Jinja2)**

In the next lab, you will learn:

- What Jinja2 templates are
- Difference between `copy` and `template`
- Using variables inside configuration files
- Dynamic file generation
- Deploying customized configuration files to multiple servers
