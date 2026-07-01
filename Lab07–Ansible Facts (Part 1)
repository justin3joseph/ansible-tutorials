# Lab 07 - Ansible Facts (Part 1)

> **Course:** Ansible for Beginners
>
> **Lab Duration:** 90 Minutes
>
> **Difficulty:** ⭐ Beginner

---

# Lab Objectives

After completing this lab, you will be able to:

- Understand what Ansible Facts are.
- Understand why Facts are important.
- View all facts collected by Ansible.
- Display specific system information.
- Understand the `gather_facts` option.
- Use the `setup` module.
- Explore hardware and operating system information of managed nodes.

---

# Prerequisites

Before starting this lab, ensure you have completed:

- Lab 01 – Environment Setup
- Lab 02 – Inventory
- Lab 03 – Ad-hoc Commands
- Lab 04 – First Playbook
- Lab 05 – Variables
- Lab 06 – Variable Files

---

# Lab Architecture

```mermaid
graph LR

A[Master Node] -->|SSH| B[Client Node]

B --> C[Collect System Information]

C --> D[Hostname]

C --> E[Operating System]

C --> F[Memory]

C --> G[CPU]

C --> H[IP Address]

C --> I[Kernel]
```

---

# What are Ansible Facts?

Imagine you log into a Linux server.

You execute commands like:

```bash
hostname
```

```bash
free -h
```

```bash
ip addr
```

```bash
uname -r
```

```bash
cat /etc/os-release
```

Each command provides one piece of system information.

Ansible can collect **all of this information automatically**.

This information is called **Facts**.

---

# Definition

**Facts are automatically collected information about a managed node.**

Facts include:

- Hostname
- Operating System
- Distribution Version
- IP Address
- Memory
- CPU
- Disk
- Network Interfaces
- Architecture
- Kernel Version
- DNS Configuration
- Mount Points
- Time Information

---

# Why are Facts Important?

Facts allow your playbooks to make intelligent decisions.

Example:

Install **nginx** only on Ubuntu.

Install **httpd** only on Red Hat.

Restart a service only if enough memory is available.

Display the hostname in a report.

Without Facts, playbooks would not know anything about the target machine.

---

# How are Facts Collected?

Ansible uses a built-in module called:

```
setup
```

Whenever a playbook starts,

Ansible automatically executes the **setup module** unless it is disabled.

---

# gather_facts

Every playbook has an option called:

```yaml
gather_facts: yes
```

This tells Ansible:

> "Before running any task, collect information about the remote machine."

---

# Example Playbook

Create a new file.

```bash
nano facts.yml
```

Paste the following.

```yaml
---
- name: Gathering Facts Demo

  hosts: servers

  gather_facts: yes

  tasks:

    - name: Display Hostname

      debug:

        var: ansible_hostname
```

Save the file.

---

# Understanding the Playbook

## hosts

```yaml
hosts: servers
```

Runs on every server inside the inventory group.

---

## gather_facts

```yaml
gather_facts: yes
```

Collects system information before executing tasks.

---

## debug

```yaml
debug:
```

Prints information.

---

## var

```yaml
var: ansible_hostname
```

Displays the value of a fact.

---

# Step 1 - Execute the Playbook

Run

```bash
ansible-playbook -i inventory.ini facts.yml
```

---

# Expected Output

```
PLAY [Gathering Facts Demo]

TASK [Gathering Facts]

ok

TASK [Display Hostname]

ok:

ansible_hostname: client-node

PLAY RECAP

client
```

---

# What Happened?

Before running your task,

Ansible automatically connected to the client node and executed the **setup module**.

The setup module collected hundreds of facts.

Your task displayed only one of them:

```
ansible_hostname
```

---

# Viewing All Facts

Instead of displaying only one fact,

let's display everything.

Modify the playbook.

```yaml
---
- name: Display All Facts

  hosts: servers

  gather_facts: yes

  tasks:

    - debug:

        var: ansible_facts
```

---

Run

```bash
ansible-playbook -i inventory.ini facts.yml
```

---

# Expected Output

```
ansible_facts:

architecture

bios_vendor

distribution

distribution_version

hostname

kernel

processor

memory

network

...

Hundreds of lines...
```

You may see **hundreds of lines of output**. This is normal because Ansible collects a large amount of information about the managed node.

---

# Understanding ansible_facts

`ansible_facts` is a dictionary that stores all collected information.

Think of it as a large database.

```
ansible_facts

|

|-- hostname

|-- kernel

|-- distribution

|-- memory

|-- cpu

|-- interfaces

|-- architecture

|-- disks

|-- dns

|-- mounts

...
```

---

# Display Individual Facts

Instead of displaying everything,

display only the information you need.

---

## Hostname

```yaml
- debug:
    var: ansible_hostname
```

Expected Output

```
client-node
```

---

## Operating System

```yaml
- debug:
    var: ansible_distribution
```

Expected Output

```
Ubuntu
```

---

## Distribution Version

```yaml
- debug:
    var: ansible_distribution_version
```

Expected Output

```
24.04
```

---

## Kernel Version

```yaml
- debug:
    var: ansible_kernel
```

Example Output

```
6.8.0-71-generic
```

---

## Architecture

```yaml
- debug:
    var: ansible_architecture
```

Example Output

```
x86_64
```

---

## Python Version

```yaml
- debug:
    var: ansible_python_version
```

Example Output

```
3.12.3
```

---

# Lab Exercise 1

Modify the playbook and display:

- Hostname
- Operating System
- Kernel Version
- Architecture
- Python Version

Run the playbook after changing each fact and observe the output.

---

# Using the setup Module (Ad-hoc Command)

You can collect facts without creating a playbook.

Run:

```bash
ansible servers -i inventory.ini -m setup
```

This displays all available facts.

---

# Filtering Facts

To display only the hostname:

```bash
ansible servers -i inventory.ini -m setup -a "filter=ansible_hostname"
```

Example Output

```
client-node
```

---

Display only the operating system:

```bash
ansible servers -i inventory.ini -m setup -a "filter=ansible_distribution"
```

---

Display only the kernel version:

```bash
ansible servers -i inventory.ini -m setup -a "filter=ansible_kernel"
```

---

Display only the architecture:

```bash
ansible servers -i inventory.ini -m setup -a "filter=ansible_architecture"
```

---

# Verification Checklist

Verify that you can:

- Run a playbook with `gather_facts`.
- Display the hostname.
- Display the operating system.
- Display the kernel version.
- Display the architecture.
- Use the `setup` module.
- Filter facts using the `filter` option.

---

## Continue to Part 2

In Part 2, you will learn:

- Memory Facts
- CPU Facts
- IP Address Facts
- Network Facts
- Formatting Fact Output
- Using Facts in `when` Conditions
- Best Practices
- Challenge Lab
- Viva Questions
- Summary
