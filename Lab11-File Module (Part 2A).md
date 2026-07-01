---

# Lab 7 - Understanding File Permissions

In Linux, every file and directory has permissions.

Permissions determine:

- Who can read a file
- Who can write to a file
- Who can execute a file

Ansible allows us to manage these permissions using the **mode** parameter.

---

# Linux Permission Basics

Consider the following output.

```bash
ls -l /tmp/app.log
```

Output

```
-rw-r--r--
```

Let's understand each part.

```
-

↓

File Type

rw-

↓

Owner

r--

↓

Group

r--

↓

Others
```

Meaning

| Permission | Value |
|------------|-------|
| r | Read |
| w | Write |
| x | Execute |

---

# Numeric Permissions

Linux also represents permissions using numbers.

| Number | Permission |
|----------|------------|
| 7 | rwx |
| 6 | rw- |
| 5 | r-x |
| 4 | r-- |
| 0 | --- |

Common modes

| Mode | Meaning |
|------|---------|
| 644 | Owner Read/Write, Others Read |
| 755 | Owner Full Access, Others Read & Execute |
| 700 | Owner Full Access Only |
| 777 | Everyone Full Access (Not Recommended) |

---

# Lab 8 - Setting File Permissions

Create a new playbook.

```bash
nano permission.yml
```

Paste the following.

```yaml
---
- name: Set File Permissions

  hosts: servers

  become: yes

  tasks:

    - name: Create Application File

      file:

        path: /tmp/application.log

        state: touch

        mode: "0644"
```

Save the file.

---

# Understanding the Playbook

```yaml
mode: "0644"
```

Meaning

```
Owner

Read + Write

Group

Read

Others

Read
```

---

# Run

```bash
ansible-playbook -i inventory.ini permission.yml
```

---

# Verify

Login to the client.

```bash
ls -l /tmp/application.log
```

Expected

```
-rw-r--r--
```

---

# Lab 9 - Creating a Directory with Permissions

Create

```bash
nano directory-permission.yml
```

Paste

```yaml
---
- name: Directory Permissions

  hosts: servers

  become: yes

  tasks:

    - name: Create Secure Directory

      file:

        path: /opt/project

        state: directory

        mode: "0755"
```

---

# Verify

```bash
ls -ld /opt/project
```

Expected

```
drwxr-xr-x
```

---

# Understanding Directory Permissions

Directories require the execute (`x`) permission.

Without execute permission,

users cannot enter the directory.

Example

```
755

↓

Owner

rwx

↓

Group

r-x

↓

Others

r-x
```

---

# Lab 10 - Changing Permissions of an Existing File

Suppose

```
/tmp/application.log
```

already exists.

Modify the playbook.

```yaml
---
- name: Change Permissions

  hosts: servers

  become: yes

  tasks:

    - name: Update Permissions

      file:

        path: /tmp/application.log

        mode: "0600"
```

---

# Run

```bash
ansible-playbook -i inventory.ini permission.yml
```

---

# Verify

```bash
ls -l /tmp/application.log
```

Expected

```
-rw-------
```

---

# Lab 11 - Changing File Owner

The File Module can change the owner of a file.

> **Prerequisite:** Ensure the user exists on the managed node. For example, if you created `devuser1` in Lab 10, you can use that account. Otherwise, create a test user first.

```yaml
---
- name: Change Owner

  hosts: servers

  become: yes

  tasks:

    - name: Change File Owner

      file:

        path: /tmp/application.log

        owner: devuser1
```

---

# Verify

```bash
ls -l /tmp/application.log
```

Expected

```
devuser1
```

appears as the owner.

---

# Lab 12 - Changing File Group

Change the group.

```yaml
---
- name: Change Group

  hosts: servers

  become: yes

  tasks:

    - name: Change File Group

      file:

        path: /tmp/application.log

        group: devuser1
```

---

# Verify

```bash
ls -l /tmp/application.log
```

Observe the group name.

---

# Lab 13 - Setting Owner, Group and Permissions Together

A common practice is to configure all properties in a single task.

```yaml
---
- name: Configure File

  hosts: servers

  become: yes

  tasks:

    - name: Configure Application File

      file:

        path: /tmp/application.log

        state: touch

        owner: devuser1

        group: devuser1

        mode: "0640"
```

---

# Verify

```bash
ls -l /tmp/application.log
```

Expected

```
-rw-r-----
```

Owner

```
devuser1
```

Group

```
devuser1
```

---

# Why Configure All Properties Together?

Instead of writing

```
Task 1

Create File

Task 2

Change Owner

Task 3

Change Group

Task 4

Change Permission
```

One task performs all operations.

This makes playbooks cleaner and more efficient.

---

# Understanding `mode`

| Mode | Owner | Group | Others |
|------|-------|-------|--------|
| 600 | rw- | --- | --- |
| 640 | rw- | r-- | --- |
| 644 | rw- | r-- | r-- |
| 700 | rwx | --- | --- |
| 755 | rwx | r-x | r-x |

---

# Best Practices

✔ Use the least permissive mode required.

✔ Avoid using `777`.

✔ Set owner and group explicitly for application files.

✔ Use `become: yes` for system files.

✔ Verify changes after running the playbook.

---

# Common Mistakes

## Missing Quotes Around Mode

Wrong

```yaml
mode: 644
```

Preferred

```yaml
mode: "0644"
```

Using quotes preserves the intended octal value and avoids YAML interpretation issues.

---

## User Does Not Exist

Wrong

```yaml
owner: developer
```

when the user has not been created.

Always verify.

```bash
id developer
```

---

## Missing Root Privileges

Changing owner or group usually requires

```yaml
become: yes
```

---

# Verification Checklist

Verify that you can:

- Set file permissions.
- Set directory permissions.
- Change owner.
- Change group.
- Configure owner, group and mode together.

---

# Lab Exercise 1

Create

```
/tmp/config.ini
```

Set

```
Owner

root

Permission

600
```

Verify using

```bash
ls -l
```

---

# Lab Exercise 2

Create

```
/opt/devops
```

Set

```
Permission

755
```

Verify using

```bash
ls -ld
```

---

# Lab Exercise 3

If you have a test user (for example, `devuser1` from Lab 10), create

```
/tmp/report.txt
```

Assign:

- Owner: `devuser1`
- Group: `devuser1`
- Permission: `640`

Verify the result.

> If you do not have a test user, create one first using the Ansible `user` module or adjust the playbook to use an existing user.

---

# Continue to Part 2B

In Part 2B, you will learn:

- Symbolic Links
- Hard Links
- Using Loops with the File Module
- Best Practices
- Troubleshooting
- Challenge Lab
- Viva Questions
- Summary
