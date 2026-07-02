# Lab 13 – Template Module (Jinja2) - Part 1A-1

> **Course:** Ansible for Beginners
>
> **Lab Duration:** 90 Minutes
>
> **Difficulty:** ⭐⭐⭐ Beginner to Intermediate

---

# Lab Objectives

After completing this lab, you will be able to:

- Understand what the Template Module is.
- Understand what Jinja2 is.
- Differentiate between the Copy Module and Template Module.
- Create your first Jinja2 template.
- Deploy a template to a managed node.
- Pass variables to a template.
- Verify the generated output.

---

# Prerequisites

Complete the following labs before starting:

- Lab 01 – Environment Setup
- Lab 02 – Inventory
- Lab 03 – Ad-hoc Commands
- Lab 04 – First Playbook
- Lab 05 – Variables
- Lab 06 – Variable Files
- Lab 07 – Facts
- Lab 08 – Registered Variables
- Lab 09 – Conditional Statements
- Lab 10 – Loops
- Lab 11 – File Module
- Lab 12 – Copy Module

---

# Lab Architecture

```mermaid
graph LR

A[Control Node]

A --> B[Template Module]

B --> C[Jinja2 Template]

C --> D[Managed Node]

D --> E[/tmp/welcome.txt]
```

---

# What is the Template Module?

The **Template Module** copies a file from the Control Node to the Managed Node.

Unlike the Copy Module,

the Template Module processes **Jinja2 variables and expressions** before copying the file.

This allows the destination file to contain different values for different managed nodes.

---

# What is Jinja2?

**Jinja2** is a powerful templating engine used by Ansible.

It allows you to insert:

- Variables
- Facts
- Conditional statements
- Loops
- Filters

inside a text file.

When Ansible runs the playbook,

it replaces these placeholders with actual values.

---

# Why Do We Need Templates?

Suppose your company has 100 Linux servers.

Each server requires a configuration file.

Only one line changes:

```
hostname=server01
```

Another server needs:

```
hostname=server02
```

Another server:

```
hostname=server03
```

Creating 100 different configuration files would be difficult.

Instead,

create one template.

```
hostname={{ inventory_hostname }}
```

Ansible generates the correct file automatically.

---

# Copy Module vs Template Module

| Copy Module | Template Module |
|-------------|-----------------|
| Copies static files | Generates dynamic files |
| File contents never change | File contents change using variables |
| No variable substitution | Supports variables |
| No Jinja2 syntax | Uses Jinja2 syntax |
| Used for static files | Used for configuration files |

---

# What is a Template File?

A template file is simply a text file containing Jinja2 expressions.

By convention,

template files use the extension

```
.j2
```

Example

```
welcome.txt.j2
```

---

# Jinja2 Variable Syntax

Variables are written using double curly braces.

Example

```jinja2
{{ variable_name }}
```

Example

```jinja2
Hello {{ username }}
```

If

```
username = Justin
```

The generated file becomes

```
Hello Justin
```

---

# Lab Directory Structure

Navigate to your Ansible project.

```bash
cd ~/ansible-labs
```

Create a templates directory.

```bash
mkdir -p templates
```

Verify.

```bash
ls
```

Expected Output

```
files

templates

inventory.ini
```

---

# Lab 1 - Create Your First Template

Navigate into the templates directory.

```bash
cd templates
```

Create a template file.

```bash
nano welcome.txt.j2
```

Paste the following.

```jinja2
Welcome to Ansible!

Student Name : {{ student_name }}

This file was generated using Jinja2.

Thank you.
```

Save the file.

---

# Understanding the Template

The text

```jinja2
{{ student_name }}
```

is a Jinja2 variable.

Ansible replaces it with the value supplied in the playbook.

---

# Lab 2 - Create the Playbook

Return to your project directory.

```bash
cd ~/ansible-labs
```

Create a playbook.

```bash
nano template-demo.yml
```

Paste the following.

```yaml
---
- name: First Template Example

  hosts: servers

  become: yes

  vars:

    student_name: Justin

  tasks:

    - name: Generate Welcome File

      template:

        src: templates/welcome.txt.j2

        dest: /tmp/welcome.txt
```

Save the playbook.

---

# Understanding the Playbook

The variable

```yaml
student_name: Justin
```

is passed to the template.

The Template Module reads

```
templates/welcome.txt.j2
```

replaces

```
{{ student_name }}
```

with

```
Justin
```

and creates

```
/tmp/welcome.txt
```

on the managed node.

---

# Execute the Playbook

```bash
ansible-playbook -i inventory.ini template-demo.yml
```

---

# Expected Output

```
PLAY [First Template Example]

TASK [Generate Welcome File]

changed: [client]

PLAY RECAP

client : ok=1 changed=1 unreachable=0 failed=0
```

---

# Verify on the Managed Node

Log in to the managed node.

```bash
ssh student@client
```

Display the generated file.

```bash
cat /tmp/welcome.txt
```

Expected Output

```
Welcome to Ansible!

Student Name : Justin

This file was generated using Jinja2.

Thank you.
```

---

# How Did This Work?

The template originally contained:

```jinja2
Student Name : {{ student_name }}
```

During playbook execution,

Ansible replaced

```jinja2
{{ student_name }}
```

with

```
Justin
```

before copying the file.

---

# Idempotency

Run the playbook again.

```bash
ansible-playbook -i inventory.ini template-demo.yml
```

Expected

```
ok: [client]
```

Since the generated file has not changed,

Ansible does not update it.

---

# Lab 3 - Modify the Variable

Edit the playbook.

```bash
nano template-demo.yml
```

Change

```yaml
student_name: Justin
```

to

```yaml
student_name: Alice
```

Run the playbook again.

```bash
ansible-playbook -i inventory.ini template-demo.yml
```

---

# Verify

```bash
cat /tmp/welcome.txt
```

Expected

```
Welcome to Ansible!

Student Name : Alice

This file was generated using Jinja2.

Thank you.
```

Notice that only the variable value changed.

The template file itself remained unchanged.

---

# Verification Checklist

Verify that you can:

- Create a `.j2` template file.
- Use a Jinja2 variable.
- Pass variables from a playbook.
- Deploy the generated file.
- Verify the output on the managed node.
- Observe idempotent behavior.

---

# Lab Exercise 1

Create a new template.

```
templates/course.txt.j2
```

Contents

```text
Course Name : {{ course }}

Trainer : {{ trainer }}
```

Create a playbook that generates:

```
/tmp/course.txt
```

using the following variables:

- `course: Ansible`
- `trainer: Justin`

Verify the generated file.

---

# Summary

In this section, you learned:

- What the Template Module is.
- What Jinja2 is.
- The difference between Copy and Template modules.
- How to create your first template.
- How to use variables inside templates.
- How Ansible generates dynamic files.

---

# Next (Part 1A-2)

In the next section, you will learn:

- Multiple variables
- Built-in variables
- Using Ansible facts in templates
- Template comments
- Jinja2 filters
- Default values
- More practical examples

---

# Lab 4 - Using Multiple Variables

In the previous lab, we used only one variable.

Real-world configuration files usually contain many values.

Example:

- Application Name
- Environment
- Port Number
- Administrator
- Version

The Template Module can replace all of them dynamically.

---

## Step 1 - Create a New Template

```bash
cd ~/ansible-labs/templates
```

Create a template.

```bash
nano application.conf.j2
```

Paste the following.

```jinja2
#####################################

Application Configuration

#####################################

Application Name : {{ app_name }}

Environment      : {{ environment }}

Port             : {{ app_port }}

Administrator    : {{ admin }}

Version          : {{ version }}
```

Save the file.

---

# Step 2 - Create the Playbook

```bash
cd ~/ansible-labs
```

```bash
nano application-template.yml
```

Paste

```yaml
---
- name: Generate Application Configuration

  hosts: servers

  become: yes

  vars:

    app_name: Inventory System

    environment: Development

    app_port: 8080

    admin: Justin

    version: 1.0

  tasks:

    - name: Generate Configuration File

      template:

        src: templates/application.conf.j2

        dest: /tmp/application.conf
```

---

## Execute

```bash
ansible-playbook -i inventory.ini application-template.yml
```

---

## Verify

```bash
cat /tmp/application.conf
```

Expected Output

```
#####################################

Application Configuration

#####################################

Application Name : Inventory System

Environment      : Development

Port             : 8080

Administrator    : Justin

Version          : 1.0
```

---

# How Variable Substitution Works

Template

```jinja2
Port : {{ app_port }}
```

Variable

```yaml
app_port: 8080
```

Generated Output

```
Port : 8080
```

Ansible performs this replacement for every variable in the template.

---

# Lab 5 - Using Built-in Variables

Ansible provides several built-in variables automatically.

One of the most useful is

```jinja2
{{ inventory_hostname }}
```

This returns the current host name from the inventory.

---

## Modify the Template

Edit

```bash
nano templates/application.conf.j2
```

Append

```jinja2
Server Name      : {{ inventory_hostname }}
```

Save the file.

---

## Execute Again

```bash
ansible-playbook -i inventory.ini application-template.yml
```

---

## Verify

```bash
cat /tmp/application.conf
```

Example

```
Server Name : client
```

If multiple hosts are in your inventory, each receives its own hostname automatically.

---

# Lab 6 - Using Ansible Facts in Templates

Facts collected by Ansible can also be used inside templates.

Examples include:

- Hostname
- Operating System
- Distribution
- Kernel Version
- IP Address
- Architecture
- Memory

---

## Update the Template

Append the following.

```jinja2
Hostname         : {{ ansible_hostname }}

Operating System : {{ ansible_distribution }}

OS Version       : {{ ansible_distribution_version }}

Kernel Version   : {{ ansible_kernel }}

Architecture     : {{ ansible_architecture }}
```

---

## Execute

```bash
ansible-playbook -i inventory.ini application-template.yml
```

---

## Verify

```bash
cat /tmp/application.conf
```

Example Output

```
Hostname         : client

Operating System : Ubuntu

OS Version       : 24.04

Kernel Version   : 6.x.x

Architecture     : x86_64
```

The values displayed depend on your managed node.

---

# Lab 7 - Template Comments

Sometimes you need comments that are only for template developers.

These comments should **not** appear in the generated file.

Jinja2 provides its own comment syntax.

Example

```jinja2
{# This comment is only visible inside the template #}
```

---

## Example

Edit the template.

```jinja2
{#
Application Configuration Template

Created for Ansible Training

#}

Application Name : {{ app_name }}
```

---

## Execute

```bash
ansible-playbook -i inventory.ini application-template.yml
```

---

## Verify

```bash
cat /tmp/application.conf
```

Notice that the comment does **not** appear in the generated file.

---

# Lab 8 - Using the Default Filter

Sometimes a variable may not exist.

Instead of generating an error,

Jinja2 can use a default value.

Syntax

```jinja2
{{ variable | default("Default Value") }}
```

---

## Example

Add

```jinja2
Database Server : {{ database | default("localhost") }}
```

---

## Execute

Since the variable

```
database
```

does not exist,

the generated file contains

```
Database Server : localhost
```

---

# Why Use Default Values?

Default values make templates more flexible.

The same template can work even when optional variables are not provided.

---

# Summary of Jinja2 Syntax

| Syntax | Purpose |
|---------|---------|
| `{{ variable }}` | Print a variable |
| `{# comment #}` | Template comment |
| `{{ variable \| default("value") }}` | Default value if variable is undefined |
| `{{ inventory_hostname }}` | Inventory hostname |
| `{{ ansible_hostname }}` | Remote host hostname |
| `{{ ansible_distribution }}` | Operating system |
| `{{ ansible_kernel }}` | Kernel version |

---

# Verification Checklist

Verify that you can:

- Use multiple variables in a template.
- Use `inventory_hostname`.
- Use Ansible facts.
- Add Jinja2 comments.
- Use the `default` filter.
- Generate the expected configuration file.

---

# Lab Exercise 2

Create a template named

```
system-info.j2
```

Display the following:

- Inventory Hostname
- Hostname
- Distribution
- Distribution Version
- Kernel
- Architecture

Generate

```
/tmp/system-info.txt
```

---

# Lab Exercise 3

Create a template named

```
student.j2
```

Variables:

- Name
- Course
- Duration
- Trainer

Generate

```
/tmp/student.txt
```

---

# Mini Challenge

Create one template that generates the following file.

```
====================================

Company : ABC Technologies

Server Name : client

Operating System : Ubuntu

Application : Inventory

Environment : Development

Version : 1.0

====================================
```

Use:

- Playbook variables
- Built-in variables
- Ansible facts

Do **not** hardcode the server name or operating system.

---

# Summary

Congratulations!

You now know how to:

- Use multiple variables in Jinja2 templates.
- Use built-in Ansible variables.
- Display Ansible facts.
- Write template-only comments.
- Apply the `default` filter.
- Generate dynamic configuration files for different hosts.

These skills form the foundation for creating reusable, host-specific configuration files in production environments.

---

# Next (Part 1B)

In the next section, you will learn:

- Jinja2 expressions
- Filters (`upper`, `lower`, `replace`, `length`, `join`)
- Mathematical operations
- String formatting
- Date and time variables
- More real-world template examples
