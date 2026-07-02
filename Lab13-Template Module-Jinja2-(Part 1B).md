---

# Lab 9 - Jinja2 Expressions and Filters

Until now, we have displayed variables exactly as they were stored.

Example

```text
{{ student_name }}
```

Output

```
Justin
```

Jinja2 provides *filters* that allow us to transform data before displaying it.

Filters can

- Convert text to uppercase
- Convert text to lowercase
- Capitalize words
- Replace characters
- Calculate string length
- Join lists
- Perform mathematical operations

A filter is applied using the pipe (`|`) symbol.

General syntax

```jinja2
{{ variable | filter }}
```

---

# Lab 10 - Using the `upper` Filter

The `upper` filter converts all characters in a string to uppercase.

## Step 1 - Create a Template

```bash
cd ~/ansible-labs/templates
```

```bash
nano filters-demo.j2
```

Paste:

```jinja2
Student Name : {{ student_name }}

Upper Case   : {{ student_name | upper }}
```

Save the file.

---

## Step 2 - Create the Playbook

```bash
cd ~/ansible-labs
```

```bash
nano filters-demo.yml
```

Paste:

```yaml
---
- name: Jinja2 Upper Filter Demo

  hosts: servers

  become: yes

  vars:

    student_name: Justin Joseph

  tasks:

    - name: Generate Filter Demo

      template:

        src: templates/filters-demo.j2

        dest: /tmp/filter-demo.txt
```

---

## Execute

```bash
ansible-playbook -i inventory.ini filters-demo.yml
```

---

## Verify

```bash
cat /tmp/filter-demo.txt
```

Expected Output

```
Student Name : Justin Joseph

Upper Case   : JUSTIN JOSEPH
```

---

# How Does `upper` Work?

Template

```jinja2
{{ student_name | upper }}
```

Variable

```yaml
student_name: Justin Joseph
```

Generated Output

```
JUSTIN JOSEPH
```

The original variable remains unchanged.

---

# Lab 11 - Using the `lower` Filter

The `lower` filter converts all characters to lowercase.

Modify the template.

```jinja2
Student Name : {{ student_name }}

Upper Case   : {{ student_name | upper }}

Lower Case   : {{ student_name | lower }}
```

---

## Execute

```bash
ansible-playbook -i inventory.ini filters-demo.yml
```

---

## Verify

```bash
cat /tmp/filter-demo.txt
```

Expected

```
Student Name : Justin Joseph

Upper Case   : JUSTIN JOSEPH

Lower Case   : justin joseph
```

---

# Lab 12 - Using the `title` Filter

The `title` filter capitalizes the first letter of every word.

Modify the playbook.

```yaml
student_name: justin joseph
```

Update the template.

```jinja2
Original : {{ student_name }}

Title    : {{ student_name | title }}
```

---

## Execute

```bash
ansible-playbook -i inventory.ini filters-demo.yml
```

---

## Verify

```bash
cat /tmp/filter-demo.txt
```

Expected

```
Original : justin joseph

Title    : Justin Joseph
```

---

# Lab 13 - Using the `capitalize` Filter

Unlike `title`, the `capitalize` filter only capitalizes the **first letter of the entire string**.

Modify the template.

```jinja2
Original   : {{ student_name }}

Capitalize : {{ student_name | capitalize }}
```

---

## Execute

```bash
ansible-playbook -i inventory.ini filters-demo.yml
```

---

## Verify

```bash
cat /tmp/filter-demo.txt
```

Expected

```
Original   : justin joseph

Capitalize : Justin joseph
```

Notice the difference:

| Filter | Output |
|---------|--------|
| `title` | Justin Joseph |
| `capitalize` | Justin joseph |

---

# Comparison of String Filters

| Filter | Input | Output |
|---------|-------|--------|
| `upper` | `Justin` | `JUSTIN` |
| `lower` | `Justin` | `justin` |
| `title` | `justin joseph` | `Justin Joseph` |
| `capitalize` | `justin joseph` | `Justin joseph` |

---

# Verification Checklist

Verify that you can:

- Use the `upper` filter.
- Use the `lower` filter.
- Use the `title` filter.
- Use the `capitalize` filter.
- Observe that filters do **not** modify the original variable.

---

# Lab Exercise 4

Create a template named:

```
employee.j2
```

Display the employee name in:

- Original format
- Uppercase
- Lowercase
- Title Case
- Capitalized

Use the variable:

```yaml
employee_name: john doe
```

Generate the file:

```
/tmp/employee.txt
```

Verify the output.

---

# Summary

In this section, you learned:

- What Jinja2 filters are.
- How to transform text using `upper`, `lower`, `title`, and `capitalize`.
- That filters affect only the generated output, not the original variable.

---

# Next (Part 1B-1B)

In the next section, you will learn:

- `replace`
- `length`
- `join`
- Mathematical expressions
- More practical examples

---

# Lab 14 - Using the `replace` Filter

The `replace` filter replaces one string with another.

This is useful when you need to:

- Modify configuration values
- Replace domain names
- Standardize file names
- Generate customized text

General syntax:

```jinja2
{{ variable | replace("old", "new") }}
```

---

## Step 1 - Modify the Template

Edit the template.

```bash
nano templates/filters-demo.j2
```

Append the following.

```jinja2
Original Company : {{ company }}

Updated Company  : {{ company | replace("ABC", "XYZ") }}
```

---

## Step 2 - Modify the Playbook

Edit

```bash
nano filters-demo.yml
```

Add a variable.

```yaml
vars:

  student_name: Justin Joseph

  company: ABC Technologies
```

---

## Execute

```bash
ansible-playbook -i inventory.ini filters-demo.yml
```

---

## Verify

```bash
cat /tmp/filter-demo.txt
```

Expected Output

```
Original Company : ABC Technologies

Updated Company  : XYZ Technologies
```

---

# Lab 15 - Using the `length` Filter

The `length` filter returns the number of characters in a string or the number of items in a list.

Example

```jinja2
{{ student_name | length }}
```

---

## Modify the Template

Append the following.

```jinja2
Student Name : {{ student_name }}

Total Characters : {{ student_name | length }}
```

---

## Execute

```bash
ansible-playbook -i inventory.ini filters-demo.yml
```

---

## Verify

Example Output

```
Student Name : Justin Joseph

Total Characters : 14
```

> **Note:** Spaces are counted as characters.

---

# Lab 16 - Using the `join` Filter

The `join` filter combines all items in a list into a single string.

---

## Modify the Playbook

Add a list variable.

```yaml
vars:

  student_name: Justin Joseph

  company: ABC Technologies

  courses:

    - Linux

    - Ansible

    - Docker

    - Kubernetes
```

---

## Modify the Template

Append.

```jinja2
Courses : {{ courses | join(", ") }}
```

---

## Execute

```bash
ansible-playbook -i inventory.ini filters-demo.yml
```

---

## Verify

```bash
cat /tmp/filter-demo.txt
```

Expected

```
Courses : Linux, Ansible, Docker, Kubernetes
```

---

# Lab 17 - Mathematical Expressions

Jinja2 can perform arithmetic operations.

Supported operators include:

| Operator | Meaning |
|----------|---------|
| `+` | Addition |
| `-` | Subtraction |
| `*` | Multiplication |
| `/` | Division |
| `%` | Modulus |

---

## Modify the Playbook

Add variables.

```yaml
vars:

  student_name: Justin Joseph

  company: ABC Technologies

  number1: 25

  number2: 5
```

---

## Modify the Template

Append.

```jinja2
Addition       : {{ number1 + number2 }}

Subtraction    : {{ number1 - number2 }}

Multiplication : {{ number1 * number2 }}

Division       : {{ number1 / number2 }}

Modulus        : {{ number1 % number2 }}
```

---

## Execute

```bash
ansible-playbook -i inventory.ini filters-demo.yml
```

---

## Verify

Expected Output

```
Addition       : 30

Subtraction    : 20

Multiplication : 125

Division       : 5.0

Modulus        : 0
```

---

# Practical Example

Suppose a company stores:

```yaml
vars:

  first_name: Justin

  last_name: Joseph
```

Template

```jinja2
Employee Name : {{ first_name }} {{ last_name }}
```

Generated Output

```
Employee Name : Justin Joseph
```

---

# Combining Filters

Filters can be chained together.

Example

```jinja2
{{ student_name | upper | replace(" ", "_") }}
```

If

```yaml
student_name: Justin Joseph
```

Output

```
JUSTIN_JOSEPH
```

---

# Summary of Filters Learned

| Filter | Purpose |
|---------|---------|
| `upper` | Convert to uppercase |
| `lower` | Convert to lowercase |
| `title` | Capitalize every word |
| `capitalize` | Capitalize first letter of the string |
| `replace` | Replace text |
| `length` | Count characters or list items |
| `join` | Combine list items into a string |

---

# Verification Checklist

Verify that you can:

- Replace text using `replace`.
- Count characters using `length`.
- Join list items using `join`.
- Perform arithmetic operations.
- Chain multiple filters together.

---

# Lab Exercise 5

Create a template named:

```
employee-report.j2
```

Variables:

```yaml
employee_name: john doe

department: Information Technology

skills:

  - Linux

  - Ansible

  - Docker

salary: 50000

bonus: 10000
```

Display:

- Employee name in uppercase
- Department in title case
- Skills joined with commas
- Total salary (`salary + bonus`)
- Number of skills

Generate:

```
/tmp/employee-report.txt
```

---

# Mini Challenge

Create a template that generates the following report dynamically.

```
====================================

Employee Information

====================================

Name          : JUSTIN JOSEPH

Department    : Information Technology

Courses       : Linux, Ansible, Docker

Total Courses : 3

Monthly Salary: 60000

====================================
```

Requirements:

- Use `upper`
- Use `title`
- Use `join`
- Use `length`
- Use arithmetic expressions

Do **not** hardcode any values.

---

# Summary

Congratulations!

You have now learned how to use Jinja2 filters and expressions to transform data before it is written to a file.

You learned to:

- Replace text
- Count characters and list items
- Join list elements
- Perform mathematical calculations
- Chain multiple filters together

These features make Jinja2 templates powerful enough to generate dynamic configuration files, reports, and application settings from a single reusable template.

---

# Next (Part 1B-2)

In the next section, you will learn:

- Date and time variables
- Formatting output
- Chaining advanced filters
- Real-world configuration templates
- Practical exercises
- Mini project
