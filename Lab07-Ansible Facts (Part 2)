---

# Lab 2 - Display Memory Information

One of the most useful facts is the amount of installed RAM.

Open the playbook.

```bash
nano facts.yml
```

Replace the task with the following.

```yaml
---
- name: Display Memory Information

  hosts: servers

  gather_facts: yes

  tasks:

    - name: Display Total Memory

      debug:

        var: ansible_memtotal_mb
```

Save the file.

Run the playbook.

```bash
ansible-playbook -i inventory.ini facts.yml
```

---

# Expected Output

```
TASK [Display Total Memory]

ok:

ansible_memtotal_mb: 3950
```

> **Note**
>
> The memory value displayed depends on your client machine.

---

# What is ansible_memtotal_mb?

This fact represents the total physical memory (RAM) installed on the managed node.

Example

| Machine | Memory |
|---------|--------|
| Ubuntu VM | 2048 MB |
| AWS EC2 | 4096 MB |
| Physical Server | 16384 MB |

---

# Lab 3 - Display CPU Information

Modify the playbook.

```yaml
---
- name: CPU Information

  hosts: servers

  gather_facts: yes

  tasks:

    - name: Display CPU Count

      debug:

        var: ansible_processor_vcpus
```

Run

```bash
ansible-playbook -i inventory.ini facts.yml
```

---

# Expected Output

```
ansible_processor_vcpus: 2
```

---

# Understanding CPU Facts

Some useful CPU facts include:

| Fact | Description |
|------|-------------|
| ansible_processor | CPU Model |
| ansible_processor_cores | Physical CPU Cores |
| ansible_processor_count | Number of CPUs |
| ansible_processor_vcpus | Virtual CPUs |

---

# Explore CPU Information

Try each of the following.

```yaml
ansible_processor
```

```yaml
ansible_processor_cores
```

```yaml
ansible_processor_count
```

```yaml
ansible_processor_vcpus
```

Observe the differences.

---

# Lab 4 - Display IP Address

Modify the playbook.

```yaml
---
- name: IP Address Demo

  hosts: servers

  gather_facts: yes

  tasks:

    - name: Display IP Address

      debug:

        var: ansible_default_ipv4.address
```

Run

```bash
ansible-playbook -i inventory.ini facts.yml
```

---

# Expected Output

```
192.168.1.20
```

---

# What is ansible_default_ipv4.address?

It displays the primary IPv4 address used by the managed node.

Equivalent Linux command:

```bash
hostname -I
```

or

```bash
ip addr
```

---

# Lab 5 - Display Multiple Facts Together

Instead of displaying one fact at a time,

let's display a complete system summary.

Replace the playbook.

```yaml
---
- name: System Information

  hosts: servers

  gather_facts: yes

  tasks:

    - name: Display System Information

      debug:

        msg:

          - "Hostname      : {{ ansible_hostname }}"

          - "Operating Sys : {{ ansible_distribution }}"

          - "Version       : {{ ansible_distribution_version }}"

          - "Kernel        : {{ ansible_kernel }}"

          - "Architecture  : {{ ansible_architecture }}"

          - "Memory        : {{ ansible_memtotal_mb }} MB"

          - "CPU           : {{ ansible_processor_vcpus }}"

          - "IP Address    : {{ ansible_default_ipv4.address }}"
```

---

Run

```bash
ansible-playbook -i inventory.ini facts.yml
```

---

# Expected Output

```
Hostname      : client-node

Operating Sys : Ubuntu

Version       : 24.04

Kernel        : 6.8.0-71-generic

Architecture  : x86_64

Memory        : 4096 MB

CPU           : 2

IP Address    : 192.168.1.20
```

---

# Understanding the Output

This playbook generates a small system report.

Such reports are useful for:

- Server inventory
- Documentation
- Auditing
- Asset management
- Troubleshooting

---

# Lab 6 - Explore Facts Using setup Module

Instead of using a playbook,

Ansible can display facts directly.

Display all facts.

```bash
ansible servers -i inventory.ini -m setup
```

---

Display only memory.

```bash
ansible servers -i inventory.ini -m setup -a "filter=ansible_memtotal_mb"
```

---

Display only CPU.

```bash
ansible servers -i inventory.ini -m setup -a "filter=ansible_processor_vcpus"
```

---

Display only IP Address.

```bash
ansible servers -i inventory.ini -m setup -a "filter=ansible_default_ipv4"
```

---

Display only Kernel.

```bash
ansible servers -i inventory.ini -m setup -a "filter=ansible_kernel"
```

---

# Lab 7 - Disable Fact Gathering

Sometimes gathering facts is unnecessary.

Example:

```yaml
---
- name: Disable Facts

  hosts: servers

  gather_facts: no

  tasks:

    - debug:

        msg: "Hello from Ansible"
```

Run

```bash
ansible-playbook -i inventory.ini facts.yml
```

Notice

There is **no "Gathering Facts" task** before execution.

---

# Why Disable Facts?

Disabling fact gathering can:

- Reduce execution time.
- Speed up automation.
- Reduce network traffic.

Use it when your playbook does not require system information.

---

# Commonly Used Facts

| Fact | Description |
|------|-------------|
| ansible_hostname | Hostname |
| ansible_distribution | Operating System |
| ansible_distribution_version | OS Version |
| ansible_kernel | Kernel Version |
| ansible_architecture | CPU Architecture |
| ansible_memtotal_mb | Total RAM |
| ansible_processor_vcpus | Virtual CPUs |
| ansible_default_ipv4.address | Primary IP Address |
| ansible_machine | Machine Type |
| ansible_python_version | Python Version |
| ansible_fqdn | Fully Qualified Domain Name |

---

# Best Practices

✔ Gather facts only when required.

✔ Display only the required facts.

✔ Use meaningful output messages.

✔ Use facts for reporting.

✔ Use facts in conditional tasks.

---

# Lab Exercise 1

Create a playbook that displays:

- Hostname
- Kernel
- CPU
- Memory
- IP Address

using one `debug` task.

---

# Lab Exercise 2

Use the `setup` module to display only:

- Hostname
- Architecture
- Python Version

without using a playbook.

---

# Challenge Lab

Create a playbook that produces the following report.

```
====================================

SERVER INFORMATION

====================================

Hostname      :

Operating Sys :

Version       :

Kernel        :

Memory        :

CPU           :

IP Address    :

Python        :

Architecture  :

====================================
```

Use only Ansible Facts.

---

# Troubleshooting

## Error

```
VARIABLE IS NOT DEFINED
```

Possible causes:

- Incorrect fact name.
- Typing mistake.
- Facts were disabled.

---

## Error

```
'gather_facts' is set to no
```

Solution

Enable

```yaml
gather_facts: yes
```

---

## Error

```
No IPv4 Address
```

Possible causes:

- Network interface is down.
- Machine has no IPv4 address.
- Incorrect fact name.

---

# Verification Checklist

Verify that you can:

- Display hostname.
- Display operating system.
- Display kernel.
- Display architecture.
- Display memory.
- Display CPU.
- Display IP address.
- Use the setup module.
- Filter facts.
- Disable fact gathering.

---

# Viva Questions

### 1. What are Ansible Facts?

---

### 2. Which module collects Facts?

---

### 3. What is `gather_facts`?

---

### 4. Which fact displays the hostname?

---

### 5. Which fact displays the IP Address?

---

### 6. Which fact displays the kernel version?

---

### 7. Which fact displays installed RAM?

---

### 8. Why do we disable fact gathering?

---

### 9. Which command displays all facts?

---

### 10. Can Facts be used in conditions?

---

# Summary

Congratulations!

In this lab, you learned how to:

- Understand Ansible Facts.
- Use `gather_facts`.
- Explore system information.
- Display Hostname.
- Display Operating System.
- Display Kernel Version.
- Display CPU Information.
- Display Memory Information.
- Display IP Address.
- Use the `setup` module.
- Filter facts.
- Disable fact gathering when not required.

These Facts make Ansible playbooks dynamic and intelligent because they allow tasks to adapt automatically based on the configuration of the managed host.

---

# Next Lab

➡ **Lab 08 – Registered Variables**

In the next lab, you will learn:

- What Registered Variables are.
- How to store command output.
- How to reuse stored values.
- Understanding `stdout`, `stderr`, `rc`, and `changed`.
- Building dynamic playbooks using registered variables.
