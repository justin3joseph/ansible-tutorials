# Lab 13 – Part 5: Real-world Examples, Best Practices, Troubleshooting, Exercises and Viva

> Course: Ansible for Beginners

# Objectives

After completing this section you will be able to:

- Build reusable Jinja2 templates
- Apply recommended template design practices
- Troubleshoot common template errors
- Create production-style configuration files
- Complete challenge exercises

---

# Real-world Example 1 – Application Configuration

## Templates

```text
Application Name : {{ app_name }}
Environment      : {{ environment }}
Server           : {{ inventory_hostname }}
OS               : {{ ansible_distribution }}

{% if environment == "Production" %}
Debug Mode       : Disabled
{% else %}
Debug Mode       : Enabled
{% endif %}

Installed Packages

{% for package in packages %}
- {{ package }}
{% endfor %}
```

## Variables

```yaml
app_name: Inventory
environment: Production

packages:
  - nginx
  - git
  - curl
```

Expected output:

```text
Application Name : Inventory
Environment      : Production
Server           : client
OS               : Ubuntu

Debug Mode       : Disabled

Installed Packages

- nginx
- git
- curl
```

---

# Real-world Example 2 – Nginx Virtual Host

```text
server {
    listen {{ port }};

    server_name {{ server_name }};

    root {{ document_root }};

    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Variables:

```yaml
port: 80
server_name: demo.example.com
document_root: /var/www/html
```

Deploy using:

```yaml
- template:
    src: templates/nginx.conf.j2
    dest: /etc/nginx/sites-available/default
```

---

# Real-world Example 3 – User Report

```text
Users

{% for user in users %}
- {{ user }}
{% endfor %}

Total Users : {{ users | length }}
```

---

# Best Practices

- Store templates in the `templates/` directory.
- Use meaningful variable names.
- Keep business logic in playbooks whenever possible.
- Avoid deeply nested `if` statements.
- Use the `default` filter for optional values.
- Reuse templates instead of creating multiple copies.
- Validate generated configuration files before restarting services.
- Test templates in a non-production environment first.

---

# Common Mistakes

| Mistake | Example | Fix |
|---------|---------|-----|
| Missing `endif` | `{% if ... %}` | Add `{% endif %}` |
| Missing `endfor` | `{% for ... %}` | Add `{% endfor %}` |
| Undefined variable | `{{ username }}` | Define the variable or use `default` |
| Incorrect indentation | YAML errors | Use consistent indentation |
| Hard-coded values | Fixed hostnames | Use variables instead |

---

# Troubleshooting

## Undefined Variable

Error:

```text
'variable_name' is undefined
```

Solution:

```text
{{ variable_name | default("value") }}
```

---

## Template Syntax Error

Check:

- Missing braces
- Missing `%}`
- Missing `endif`
- Missing `endfor`

Validate with:

```bash
ansible-playbook --syntax-check playbook.yml
```

---

## Generated File Not Updated

Possible causes:

- Variable unchanged
- Wrong template path
- Wrong destination path

Run again with:

```bash
ansible-playbook -i inventory.ini playbook.yml --diff
```

---

## Debug Variables

```yaml
- debug:
    var: ansible_distribution

- debug:
    var: inventory_hostname

- debug:
    var: packages
```

---

# Mini Project

Create a template named:

```text
templates/server-summary.j2
```

Display:

- Hostname
- Operating System
- Kernel Version
- Architecture
- Environment
- Installed Packages
- Total Packages

Requirements:

- At least one `if`
- One `for` loop
- One filter
- One Ansible fact

Generate:

```text
/tmp/server-summary.txt
```

---

# Challenge Lab

Create a deployment report.

Variables:

```yaml
application: Inventory
version: 2.1
environment: Production

services:
  - nginx
  - ssh
  - docker
```

Requirements:

- Display application information.
- Print all services.
- Show total number of services.
- Display "Production Ready" only when the environment is Production.

---

# Verification Checklist

- Created reusable templates
- Used variables
- Used facts
- Applied filters
- Used conditions
- Used loops
- Generated configuration files
- Verified output

---

# Viva Questions

1. What is the Template module?
2. What is Jinja2?
3. Difference between Copy and Template modules?
4. What are Jinja2 filters?
5. Explain `default`.
6. Explain `join`.
7. Explain `length`.
8. Difference between `{{ }}` and `{% %}`?
9. Explain `if`, `elif` and `else`.
10. Explain `for` loops.
11. How do you access Ansible facts?
12. Why are templates preferred over static configuration files?
13. What is idempotency?
14. How do you troubleshoot undefined variables?
15. Name five common Jinja2 filters.

---

# Summary

In Lab 13 you learned:

- Template module fundamentals
- Jinja2 syntax
- Variables
- Built-in variables
- Ansible facts
- String and list filters
- Arithmetic expressions
- Conditional statements
- Loops
- Dynamic configuration generation
- Troubleshooting techniques
- Best practices

You can now create reusable, maintainable templates for real-world infrastructure automation.

---

# Next Lab

**Lab 14 – Package Module**

Topics:

- apt
- yum
- dnf
- package module
- Installing packages
- Updating packages
- Removing packages
- Hands-on exercises
