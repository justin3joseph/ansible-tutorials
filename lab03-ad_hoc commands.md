# What is an Adhoc Command?
An ansible ad-hoc command is a one-line command that helps you execute simple tasks in a simple yet efficient manner without the need of creating playbooks. Such tasks include copying files between hosts, rebooting servers, adding & removing users and installing a single package.

A playbook is a set of configuration management scripts that define how tasks are to be executed on remote hosts or a group of host machines. The scripts or instructions are written in YAML format.

Sometimes, you may want to perform quick and simple tasks on remote hosts or servers in Ansible without necessarily having to create a playbook. In that case, you would require to run an ad-hoc command.

# Basic Usage of Adhoc Commands
In this tutorial, we explore various applications of Ansible Ad-Hoc commands. We are going to use the inventory file below for a demonstration.
```
[webservers]
192.168.1.51

[database_servers]
192.168.1.52
```
The most basic usage of Ansible-Adhoc commands is pinging a host or a group of hosts.
```
ansible -m ping all
```
In the above command, the ```-m``` parameter is the module option. Ping is the adhoc command and the second parameter ```all``` represents all hosts in the inventory file. The output of the command is shown below:

To ping, a particular group of hosts, replace ‘all’ parameter with the group name. In the example below, we are testing connectivity with hosts under the webservers group.
```
ansible -m ping webservers
```
Additionally, you can use the -a attribute to specify regular Linux commands in double quotation marks. For example, to check system uptime of remote systems, run:
```
ansible -a "uptime" all
```
To check disk usage of remote hosts run.
```
ansible -a "df -Th" all
```
There are hundreds upon hundreds of modules that you can use with Adhoc command. To view the entire list of modules with their descriptions, run the command below.
```
ansible-doc -l
```
To view detailed information about a particular module, run the command.
```
ansible-doc module_name
```
For example, to search for more details about the yum module run:
```
ansible-doc yum
```
# Managing Packages
Ansible adhoc commands can be used for the installation and removal of packages using yum and apt package managers.

To install Apache web server on the CentOS 7 host under webservers group in the inventory file run the command:
```
ansible webservers -m yum -a "name=httpd  state=present"
```
To verify the installation of the Apache web server, log in to the remote client and run.
```
rpm -qa | grep httpd
```
To uninstall Apache, simple change the state from present to absent.
```
ansible webservers -m yum -a "name=httpd  state=absent"
```
Again, to confirm the removal of httpd run.
```
rpm -qa | grep httpd
```

# Privilege Escalation
If you are running Ansible as a regular user, Ansible provides privilege escalation in remote hosts using the **--become** option to acquire root privileges and **-k** to prompt for the password.

For example, to run the Ansible adhoc command ```netstat -pnltu``` with the privileged option **–-become** and option **-K** to prompt for the root user’s password to run the command.
```
ansible webservers -m shell -a 'netstat -pnltu' --become -K
```
To become another user other than root, use the **--become-user** attribute.

For example to run **df -Th** as ansible user on the remote hosts and prompt for the password run:
```
ansible all -m shell -a 'df -Th' --become-user ansible -K
```
Install ```httpd``` in webserver groups
```
ansible webservers -m yum -a "name=httpd  state=present --become -K"
```
 # Service Management with Ansible - ```service```
 To start a Service using ansible run
 ```
 ansible -i inventory -m service -a "name=httpd  state=started" webserver --become -K
 ```
To enable a service while Starting
```
ansible -i inventory -m service -a "name=httpd  state=started enabled=true" dbserver --become -K
```
To stop the service
```
ansible -i inventory -m service -a "name=httpd  state=stopped enabled=true" dbserver --become -K
```

# Creating Users and Groups Using Ansible
When creating users, the ‘user‘ module comes in handy. To create a new user student on the client system webserver, issue the command.
```
ansible -i inventory webserver -m user -a "name=student state=present createhome=yes" --become -K
```
To confirm the creation of the new user, run the command:
```
ansible -i inventory webserver -a "id student"
or
ansible -i inventory webserver -a "tail /etc/passwd"
```
To remove the user, run the command:
```
ansible -i inventory webserver -m user -a "name=student state=absent"
```

# Gathering Facts about Host Systems
Facts refer to detailed information about a system. This includes information about the IP address, system architecture, memory, and CPU to mention a few.

To retrieve information about remote hosts, run the command:
```
ansible all -m setup
```
# File Transfer / Copy Files
Ansible uses the module copy to securely copy files from the Ansible control to multiple remote hosts.

Below is an example of a copy operation:
```
ansible webservers -m copy -a "src=/var/log/secure dest=/tmp/"
```
Additionally, you can append the owner and group arguments as shown:
```
ansible webservers -m file -a "dest=/tmp/secure mode=600 owner=ansible group=ansible"
```
You can also create directories, in a similar manner to ```mkdir -p``` as shown.
```
ansible webservers -m file -a "dest=/path/to/directory mode=755 owner=ansible group=ansible state=directory"
```
# Conclusion
In this article, we shed light on how you can configure managed nodes to run Ansible ad-hoc commands to manage remote hosts. We do hope you found it useful.
