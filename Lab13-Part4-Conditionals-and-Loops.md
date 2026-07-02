# Lab 13 – Part 4: Jinja2 Conditionals and Loops

> **Course:** Ansible for Beginners

## Objectives

After this lab you will be able to:

- Use `if`, `elif` and `else` in Jinja2 templates.
- Compare variables using logical operators.
- Loop through lists.
- Loop through dictionaries.
- Generate dynamic configuration files.

---

# Prerequisites

- Labs 1–12 completed
- Lab 13 Parts 1–3 completed
- One control node and one managed node
- `template` module working correctly

---

# Theory

Jinja2 supports control structures.

Unlike `{{ }}` (used to print values), control structures use:

```text
{% ... %}
```

Common structures:

```text
{% if %}
{% elif %}
{% else %}
{% endif %}

{% for %}
{% endfor %}
```

---

# Lab 1 – Simple if Statement

## Template

Create:

```bash
nano templates/environment.j2
```

```text
Application : Inventory

{% if environment == "Production" %}
Logging : Enabled
{% endif %}
```

## Playbook

```yaml
---
- name: Jinja2 If Demo
  hosts: servers
  become: true

  vars:
    environment: "Production"

  tasks:
    - template:
        src: templates/environment.j2
        dest: /tmp/environment.conf
```

Run:

```bash
ansible-playbook -i inventory.ini environment.yml
```

Verify:

```bash
cat /tmp/environment.conf
```

Expected:

```text
Application : Inventory

Logging : Enabled
```

Change:

```yaml
environment: "Development"
```

Expected:

```text
Application : Inventory
```

---

# Lab 2 – if / else

Template:

```text
Environment : {{ environment }}

{% if environment == "Production" %}
Debug Mode : Disabled
{% else %}
Debug Mode : Enabled
{% endif %}
```

Test with:

```yaml
environment: "Production"
```

and

```yaml
environment: "Development"
```

Observe the different outputs.

---

# Lab 3 – if / elif / else

```text
{% if environment == "Development" %}
Environment Type : Development
{% elif environment == "Testing" %}
Environment Type : Testing
{% elif environment == "Production" %}
Environment Type : Production
{% else %}
Environment Type : Unknown
{% endif %}
```

Try:

- Development
- Testing
- Production
- Staging

---

# Comparison Operators

| Operator | Meaning |
|----------|---------|
| == | Equal |
| != | Not equal |
| > | Greater than |
| < | Less than |
| >= | Greater than or equal |
| <= | Less than or equal |

Example:

```text
{% if port > 1024 %}
Application Port : {{ port }}
{% endif %}
```

---

# Logical Operators

## and

```text
{% if os == "Ubuntu" and environment == "Production" %}
Deploy Ubuntu Production Configuration
{% endif %}
```

## or

```text
{% if environment == "Testing" or environment == "Development" %}
Verbose Logging Enabled
{% endif %}
```

## not

```text
{% if not maintenance %}
System Available
{% endif %}
```

---

# Lab 4 – for Loop

Variables:

```yaml
packages:
  - nginx
  - git
  - curl
  - unzip
```

Template:

```text
Packages

{% for package in packages %}
- {{ package }}
{% endfor %}
```

Expected:

```text
Packages

- nginx
- git
- curl
- unzip
```

---

# Lab 5 – Numbered List

Template:

```text
Installed Packages

{% for package in packages %}
Package : {{ package }}
{% endfor %}
```

---

# Lab 6 – Loop Through Dictionary

Variables:

```yaml
users:
  john:
    shell: /bin/bash
  alice:
    shell: /bin/sh
```

Template:

```text
Users

{% for user, details in users.items() %}
User : {{ user }}
Shell: {{ details.shell }}

{% endfor %}
```

Expected:

```text
Users

User : john
Shell: /bin/bash

User : alice
Shell: /bin/sh
```

---

# Real-world Example

```text
Server : {{ inventory_hostname }}

{% if ansible_distribution == "Ubuntu" %}
Package Manager : apt
{% else %}
Package Manager : yum
{% endif %}

Installed Packages

{% for package in packages %}
- {{ package }}
{% endfor %}
```

---

# Verification Checklist

- Created templates with if statements.
- Used else and elif.
- Compared variables.
- Used logical operators.
- Looped through a list.
- Looped through a dictionary.

---

# Exercise

Create `server-report.j2` displaying:

- Hostname
- Operating System
- Environment
- Installed packages
- Maintenance status

Requirements:

- Use one `if`
- One `else`
- One `for` loop

Generate:

```text
/tmp/server-report.txt
```

---

# Best Practices

- Keep templates readable.
- Avoid deeply nested conditions.
- Use variables instead of hard-coded values.
- Use playbooks for complex logic.
- Test templates with multiple variable values.

---

# Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| Undefined variable | Missing variable | Define it or use `default` |
| Loop prints nothing | Empty list | Verify variable contents |
| Wrong output | Incorrect condition | Check comparison values |
| Template syntax error | Missing `{% endif %}` or `{% endfor %}` | Validate template blocks |

---

# Viva Questions

1. Difference between `{{ }}` and `{% %}`?
2. Why use `if` in templates?
3. Difference between `if` and `elif`?
4. How do you loop through a list?
5. How do you loop through a dictionary?
6. What are logical operators?
7. When should complex logic stay in the playbook?

---

# Summary

You learned to:

- Use `if`, `elif`, and `else`
- Compare values
- Use logical operators
- Loop through lists
- Loop through dictionaries
- Generate dynamic files based on variables

These techniques are essential for creating reusable, environment-aware configuration templates.
