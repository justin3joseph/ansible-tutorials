# What is Ansible Inventory File?
Red Hat Ansible Automation Platform works against a list of managed nodes or hosts in your infrastructure that are logically organized, using an inventory file. By using an inventory file, Ansible can manage a large number of hosts with a single command. Inventories also help you use Ansible more efficiently by reducing the number of command line options you have to specify.

The inventory file can be in one of many formats, depending on the inventory plugins that you have. The most common formats are INI and YAML. Inventory files listed in this document are shown in INI format.

# Default location of inventory file
```
$ /etc/ansible/hosts
```
Ansible inventory file allows system administrators to keep track of their managed remote systems. The default inventory file is known as the hosts file and is located in the /etc/ansible directory. This is where all the managed remote nodes are specified.

```
sudo nano /etc/ansible/hosts
```
```
# This is the default ansible 'hosts' file.
#
# It should live in /etc/ansible/hosts
#
#   - Comments begin with the '#' character
#   - Blank lines are ignored
#   - Groups of hosts are delimited by [header] elements
#   - You can enter hostnames or ip addresses
#   - A hostname/ip can be a member of multiple groups

# Ex 1: Ungrouped hosts, specify before any group headers:

## green.example.com
## blue.example.com
## 192.168.100.1
## 192.168.100.10

# Ex 2: A collection of hosts belonging to the 'webservers' group:

## [webservers]
## alpha.example.org
## beta.example.org
## 192.168.1.100
## 192.168.1.110
```
By default, all entries are commented out and no hosts are specified. In the next step, you will connect to the remote hosts and create a custom inventory file.

## Create Custom Inventory File
Ansible uses the default inventory file that is located at /etc/ansible/hosts to reference managed nodes, unless you specify a custom inventory file through the ```-i``` option.

The default inventory file works just fine. In fact, you can put your remote nodes in it using their IP addresses, as shown below:

```
192.168.1.51
192.168.1.52
```
You can then execute a ping module on the remote nodes without explicitly specifying the remote hosts:
```
sudo ansible -m ping all
```
Sure, this works without any glitches, but when faced with multiple managed nodes under different projects, the best practice is to create separate inventory files for each project. This way you will facilitate resource tracking and collaborate with other sysadmins more easily without having everyone tangled up in a single hosts file.

Now, let’s create a custom inventory file. To do this, you should first create a project directory and navigate into it:

```
mkdir project_dir && cd project_dir
```

Next, create a simple text file. You can give it any arbitrary name:

```
nano inventory
```
Then list your managed nodes per line using their IP addresses:
```
192.168.1.51
192.168.1.52
```
Save the changes and exit the file. Now you can verify your managed hosts using the ansible-inventory command. Note that you must reference the full path to the inventory file when using the -i option.

```
ansible-inventory -i project_dir/inventory --list
```
From here, you can execute a playbook or a ping module on your managed hosts using the custom inventory file:

```
sudo ansible -i project_dir/inventory -m ping all
```
# Configure Host Aliases
Aliases are a simple way of referencing your managed nodes. Just like nicknames, they help you easily identify your resources without having to recall complicated names when running playbooks.

To define an alias, simply specify an alias name followed by a variable name corresponding to the hostname or IP of the remote host.

# alias variable_name

For example:
```
target_node1   ansible_host=198.168.1.51
target_node2   ansible_host=198.168.1.52
```
For instance, in the previous section we used the *ansible_host* variable that simply told Ansible where to search for the IP address of a managed node.
Then verify the inventory list to check if the nodes are referenced by aliases.

```
ansible-inventory -i project_dir/inventory --list
ansible -i inventory -m ping target_node1
ansible -i inventory -m ping all
ansible -i project_dir/inventory -m setup webservers
ansible -i project_dir/inventory -m setup webservers -a "filter=*ipv4"

```

In inventory files you can use variables to define hostnames, connection type, ports, etc. In the example below, we have gone a step further to define the user used to initiate the remote connection to the nodes.

In this case, we have two variables: ansible_host that specifies the IP of the hosts, and ansible_user that specifies the user used to connect with the remote hosts.
```
target_node1 ansible_host=198.168.1.51 ansible_user=root
target_node2 ansible_host=198.168.1.52 ansible_user=ansible
```
# Organize Nodes Into Groups and Sub-groups
For a cleaner inventory file and easier management of your managed nodes, it is usually recommended to organize them into groups and subgroups.

A host can belong to one or multiple groups. In the example below, we have grouped our 2 hosts under the webservers group in an INI format.

The inventory file below provides a better illustration of multiple servers grouped into various groups such as webservers, load_balancers and db_servers.

```
[webservers]
198.148.118.68
198.148.118.129
198.148.118.150
198.148.118.175

[load_balancers]
198.148.118.100
198.148.118.200

[db_servers]
198.148.118.50
198.148.118.60
```
To ping, a particular group of hosts, replace ‘all’ parameter with the group name. In the example below, we are testing connectivity with hosts under the webservers group.
```
ansible -m ping webservers
```
Once again, run the ansible-inventory command and you should see a similar arrangement to what we have here:
```
ansible-inventory -i project_dir/inventory --list
```
You can also define multiple groups as **'children'** groups under a **'parent'** group. In this scenario, the 'parent' group becomes the metagroup. Here is an illustration of how you can reorganize the previous inventory using metagroups:
