---

# Lab 11 - Registering Loop Results

In Lab 08, you learned how to register the output of a single task.

When a task contains a loop, Ansible stores the output of **every iteration** inside the registered variable.

This allows you to inspect the result of each item processed by the loop.

---

## Step 1 - Create a Playbook

```bash
nano loop-register.yml
```

Paste the following.

```yaml
---
- name: Register Loop Results

  hosts: servers

  tasks:

    - name: Display Package Names

      debug:

        msg: "{{ item }}"

      loop:

        - git

        - curl

        - vim

      register: package_output
```

Save the file.

---

## Step 2 - Execute the Playbook

```bash
ansible-playbook -i inventory.ini loop-register.yml
```

---

## Step 3 - Display the Registered Variable

Modify the playbook.

```yaml
---
- name: Register Loop Results

  hosts: servers

  tasks:

    - name: Display Package Names

      debug:

        msg: "{{ item }}"

      loop:

        - git

        - curl

        - vim

      register: package_output

    - name: Show Registered Output

      debug:

        var: package_output
```

Run again.

---

## Expected Output

```
package_output:

changed: false

results:

- item: git

  msg: git

- item: curl

  msg: curl

- item: vim

  msg: vim
```

---

# Understanding Registered Loop Output

Unlike a normal registered variable,

a loop stores the results in a list.

```
package_output

|

|-- results

      |

      |-- item = git

      |-- item = curl

      |-- item = vim
```

Each loop iteration has its own result.

---

# Lab 12 - Combining Loop with `when`

Loops and conditions are commonly used together.

Example:

Install packages only on Ubuntu systems.

```yaml
---
- name: Install Packages on Ubuntu

  hosts: servers

  become: yes

  gather_facts: yes

  vars:

    packages:

      - git

      - curl

      - tree

  tasks:

    - name: Install Packages

      apt:

        name: "{{ item }}"

        state: present

      loop: "{{ packages }}"

      when: ansible_distribution == "Ubuntu"
```

---

## Expected Output

If the managed node is Ubuntu,

all packages are installed.

If it is another distribution,

the task is skipped.

---

# Lab 13 - Loop Through Numbers

A loop can contain numbers.

```yaml
---
- name: Number Loop

  hosts: servers

  tasks:

    - debug:

        msg: "Number: {{ item }}"

      loop:

        - 10

        - 20

        - 30

        - 40
```

---

## Expected Output

```
Number: 10

Number: 20

Number: 30

Number: 40
```

---

# Lab 14 - Loop Through Mixed Values

A loop can also contain different types of values.

```yaml
---
- name: Mixed Loop

  hosts: servers

  tasks:

    - debug:

        msg: "{{ item }}"

      loop:

        - Linux

        - 100

        - Docker

        - 3.14
```

Although valid, keeping similar data types together improves readability.

---

# Real-World Example

Suppose every new server must have:

Packages

```
git

curl

vim
```

Directories

```
/opt/scripts

/opt/logs
```

Users

```
developer

tester
```

Without loops:

- 3 package tasks
- 2 directory tasks
- 2 user tasks

Total: **7 tasks**

With loops:

- 1 package task
- 1 directory task
- 1 user task

Total: **3 tasks**

This makes playbooks easier to maintain and extend.

---

# Best Practices

✔ Use loops whenever the same task repeats.

✔ Store lists in variables.

✔ Use meaningful variable names such as:

```yaml
packages

users

directories
```

✔ Combine loops with conditions only when necessary.

✔ Verify the outcome after running the playbook.

---

# Common Mistakes

## Missing `item`

Wrong

```yaml
name: nginx
```

inside a loop.

Correct

```yaml
name: "{{ item }}"
```

---

## Incorrect Variable Name

Wrong

```yaml
loop: "{{ package }}"
```

Correct

```yaml
loop: "{{ packages }}"
```

---

## Missing `become`

Installing packages or creating users usually requires:

```yaml
become: yes
```

---

# Troubleshooting

## Error

```
'item' is undefined
```

Possible causes:

- `loop` is missing.
- Incorrect indentation.
- Typographical error.

---

## Package Installation Failed

Check:

```bash
ansible all -i inventory.ini -m ping
```

Verify:

- SSH connectivity
- Sudo privileges
- Internet access (if required)

---

## User Not Created

Verify with:

```bash
id username
```

If not found:

- Ensure `become: yes` is present.
- Check for typing mistakes.

---

# Verification Checklist

Verify that you can:

- Register loop results.
- Understand the `results` list.
- Combine loops with `when`.
- Install multiple packages.
- Create multiple users.
- Create multiple directories.
- Create multiple files.

---

# Lab Exercise 1

Create a variable called:

```yaml
services
```

Store:

```
nginx

mysql

docker
```

Display each service using a loop.

---

# Lab Exercise 2

Create the following directories:

```
/data

/backup

/logs
```

Use one task and a loop.

---

# Lab Exercise 3

Create three users:

```
student1

student2

student3
```

Verify each user using the `id` command.

---

# Challenge Lab

Create a single playbook that performs the following:

1. Install the packages:

```
git

curl

tree
```

2. Create the directories:

```
/opt/dev

/opt/scripts

/opt/logs
```

3. Create the users:

```
developer

tester
```

4. Register the output of each loop.

5. Display a completion message.

Example:

```
====================================

SYSTEM CONFIGURATION COMPLETED

Packages Installed

Directories Created

Users Created

====================================
```

---

# Viva Questions

### 1. What is a loop in Ansible?

---

### 2. Which keyword creates a loop?

---

### 3. What does the `item` variable represent?

---

### 4. Can a loop use variables?

---

### 5. Can a loop be combined with `when`?

---

### 6. How are loop results stored when using `register`?

---

### 7. Why are loops preferred over repetitive tasks?

---

### 8. Which modules commonly use loops?

---

### 9. Why do package and user tasks often require `become: yes`?

---

### 10. What is the benefit of storing lists in variables?

---

# Summary

Congratulations!

In this lab, you learned:

- Why loops are important.
- How to use the `loop` keyword.
- How the `item` variable works.
- Installing multiple packages with a loop.
- Creating multiple users, directories, and files.
- Registering loop results.
- Combining loops with `when`.
- Best practices for writing clean, reusable playbooks.

Loops are one of the most frequently used features in Ansible because they eliminate repetitive code and make automation simpler, more scalable, and easier to maintain.

---

# Next Lab

➡ **Lab 11 – File Module**

In the next lab, you will learn:

- Managing files and directories.
- Creating, deleting, and modifying files.
- Setting file permissions.
- Changing ownership.
- Using the `file` module effectively in automation.
