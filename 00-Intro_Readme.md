# Ansible Documentation for Beginners
### Written by Justin joseph, M.E., CSE, Opensource Evangelist. Mewije Info Technics, Thrissur, Kerala

**What is Ansible ?**

Ansible is described as "simple IT automation." It's an agentless tool, meaning you don't have to install anything on the systems you are controlling. With Ansible, you can install software, configure system settings and features, and do all the things system administrators do. You know, the "operations" side of the team.

![ansible_arch_](ansible_arch_.png)

**What does Ansible do?**
To put it in the simplest terms, Ansible lets you do things remotely that you would otherwise do at the command line. Specifically, it's used to install software and change system settings. It puts a machine into the state in which you want it to remain and keeps it there.

For example, you can install (and maintain) a given version of a library on a select group of servers across your organization. You might want `Python 3.8` on all your Red Hat Enterprise Linux machines running in AWS. Ansible is perfect for that.

```
---
- name: Install nginx
  hosts: host.name.ip
  become: true

  tasks:
  - name: Add epel-release repo
    yum:
      name: epel-release
      state: present

  - name: Install nginx
    yum:
      name: nginx
      state: present

  - name: Insert Index Page
    template:
      src: index.html
      dest: /usr/share/nginx/html/index.html

  - name: Start NGiNX
    service:
      name: nginx
      state: started

```

I can think of four reasons why you, as a developer, should care about Ansible:

  - You can use it to set up small environments.
  - You can use it to make sure the correct prerequisites are installed.
  - You can be a catalyst for real DevOps culture at work.
  - You can use it for yourself.
