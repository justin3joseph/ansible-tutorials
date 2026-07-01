# Ansible Plays and Playbooks
A playbook is a set of configuration management scripts that define how tasks are to be executed on remote hosts or a group of host machines. The scripts or instructions are written in YAML format.

# Modules
Modules are discrete units of code used in playbooks for executing commands on remote hosts or servers. Each module is followed by an argument.

The basic format of a module is key: value.

```
- name: Install apache packages
    yum:   name=httpd  state=present
```
In the above YAML code snippet, **-name** and **yum** are modules.

# Plays
An ansible play is a script or an instruction that defines the task to be carried out on a server. A collection of plays constitute a playbook. *In other words, a playbook is a collection of multiple plays*, each of which clearly stipulates the task to be carried out on a server. Plays exist in YAML format.

The objective of this subtopic is to give you an overview of various tasks that can be accomplished by Ansible modules.
```
Playbook: A single YAML file.
  Play: Defines a set of activities (tasks) to be run on host.
    Task:  An action to be performed on the host
     - Execute a command
     - Run a script
     - Install a Package
     - Shutdown/Restart
```

# Lab 1:
 Execute a date command on localhost
```
---
- name: 'Execute a date command on localhost'
  hosts: localhost
  become: yes
  tasks:
     - name: 'Execute a date command'
       command: date
```
```
ansible-playbook -i inventory playbook.yaml
```

# lab 2:
 Update the playbook yaml file to add a task **name** "Task to display hosts file for the existing task."
```
---
- name: 'Execute a command to display hosts file on localhost'
  hosts: localhost
  become: yes
  tasks:
     - name: 'Task to display hosts file'
       command: 'cat /etc/hosts'
```
Run the playbook.

# Lab 3:
 Now update it to add another task. The new task must execute the command cat /etc/resolv.conf and set its name to Task to display nameservers.
```
---
- name: 'Execute two commands on localhost'
  hosts: localhost
  become: yes
  tasks:
     - name: 'Execute a date command'
       command: date
     - name: 'Task to display nameservers'
       command: 'cat /etc/resolv.conf'
```

# Lab 4.
 So far, we have been running all tasks on localhost. We would now like to run these tasks on target nodes. this host is already defined in inventory file. Update the playbook to run the tasks on the target1 host.
```
---
- name: 'Execute two commands on webserver'
  hosts: webserver
  tasks:
     - name: 'Execute a date command'
       command: date
     - name: 'Execute a command to display hosts file'
       command: 'cat /etc/hosts'
```
Run the playbook. ```ansible-playbook -i inventory playbook.yaml```

# Lab 5
Update the playbook.yaml to add a new play named Execute a command on dbserver, and a task under it to execute cat /etc/hosts command on dbserver host, name the task Task to display hosts file on node02.
```
---
- name: 'Execute two commands on node01'
  hosts: node01
  become: yes
  tasks:
    - name: 'Execute a date command'
      command: date
    - name: 'Task to display hosts file on node01'
      command: 'cat /etc/hosts'
- name: 'Execute a command on node02'
  hosts: node02
  become: yes
  tasks:
     - name: 'Task to display hosts file on node02'
       command: 'cat /etc/hosts'
```

# Lab 6
Install apache in webserver groups
```
---
- name: 'Intro to ansible playbook'
  hosts: webserver
  tasks:
   - name: 'Install httpd webserver service in target_nodes'
     yum:
       name: httpd
       state: latest
```
# Lab 7
update the playbook to enabled the httpd service
```
---
- name: 'Intro to ansible playbook'
  hosts: webserver
  tasks:
   - name: 'Install httpd webserver service in target_nodes'
     yum:
       name: httpd
       state: latest
   - name: 'Start the httpd service'
     service:
       name: httpd
       state: restarted
```

# Lab 8:
Restarting a Network Interface enp2s0
```
---
- name: Restart network service for interface eth0
  service:
    name: network
    state: restarted
    args: enp3s0
```

# Lab 9 - Copy Module
As the name suggests, copy module copies files from one location on the remote machine to a different location on the same machine.

Example 1: Copying Files from Local to Remote Linux
```
---
- name: Copy file with owner and permissions
  copy:
    src: /etc/files/abcd.conf
    dest: /opt/abcd.conf
    owner: ansible
    group: ansible
    mode: '0644'
```
Example 2: Copying Files from Local to Remote Linux
```
---
- name: Copy file with owner and permissions
  copy:
    src: /etc/files/abcd.conf
    dest: /opt/abcd.conf
    owner: ansible
    group: ansible
    mode: u=rw, g=r, o=r
```
The permissions in the previous examples can be represented as shown in the last line, The user is assigned read and write permissions, the group is assigned write permissions, and the rest of the world is assigned read permissions.

# Lab 10 - File Module
The file module is used to carry many file operations including creating files & directories, assigning file permissions, and setting symlinks.

Example 1: Perform Linux File Permissions
```
---
- name: Change file ownership, group, and permissions
  file:
    path: /etc/abcd.conf
    owner: ansible
    group: ansible
    mode: '0644'
```
The above play creates a file called abcd.conf in the /etc directory setting permissions to 0644.

Example 2: Delete Linux File
```
---
- name: Remove file (delete file)
  file:
    path: /etc/abcd.conf
    state: absent
```
This removes or deletes the file abcd.conf.

Example 3: Create a Directory
```
---
- name: create a directory if it doesn’t exist
  file:
    path: /etc/mydirectory
    State: directory
```
This will create a directory in the /etc directory setting permissions to 0777.

# Lab 11 Lineinfile Module
The lineinfile module is helpful when you want to change a single line in a file. It can replace an existing line.

Example 1: Alter Files in Linux
```
---
- name: Add a line to a file if the file does not exist, without passing regexp
  lineinfile:
    path: /etc/hosts
    line: 192.168.1.50 abcd.com
    create: yes
```
This adds the entry 192.168.1.50 abcd.com to the /etc/hosts file.

Example 2: Manipulate Files in Linux
```
---
 - name: Ensure SELinux is set to enforcing mode
  lineinfile:
    path: /etc/selinux/config
    regexp: '^SELINUX='
    line: SELINUX=disabled
```
The play above sets SELINUX value to disabled.

# Lab 11 - Archive Module
An Archive module is used for the creation of a compressed archive of a single or multiple files. It assumes the compression source exists is present on the target destination. After archival, the source file can later be deleted or removed using the statement **remove=True**.

Example 1: Create a Archive File

```
- name: Compress directory /path/to/db_dir/ into /path/to/db_backup.tgz
  archive:
    path: /path/to/db_dir
    dest: /path/to/db_backup.tgz
```
Example 2: Create a Archive File and Remove
```
- name: Compress regular file /path/to/db_dir into /path/to/db_backup.gz and remove it
  archive:
    path: /path/to/db_dir
    dest: /path/to/db_backup.tgz
    remove: yes
```
In the above play, the source file /path/to/db_dir is deleted after the archival is complete.

Example 3: Create a Archive File
```
- name: Create a bz2 archive of /path/to/db_dir
  archive:
    path: /path/to/db_dir
    format: bz2
```

# Lab 13 - Command Module
One of the most commonly used modules, the command module takes the command name and later followed by a list of arguments. The command is a passed the same way that you’d type in a Linux shell.

Example 1: Run a Command
```
- name: Executing a command using the command module
  command: cat helloworld.txt
```

Example 2: Check Uptime of Remote Linux
```
---
 - name: Check the remote host uptime
    hosts: servers
    tasks:
      - name: Execute the Uptime command over Command module
        register: uptimeoutput
        command: "uptime"

- debug:
          var: uptimeoutput.stdout_lines
```
The command module retrieves the uptime of remote servers.
