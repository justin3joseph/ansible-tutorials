# Install Ansible on CentOS7

Ansible is a zero configuration server management utility. It is used to manage many servers from a central computer. It makes every system administrative tasks easy.
In this article, I will show you how to install Ansible on CentOS 7. Let’s get started.

## Installing Ansible
Ansible is not available on the official repository of CentOS 7. But it is available in the epel repository.

So first, you have to enable epel repository in CentOS 7. The easiest way to do that is to install epel-release package using yum.

Install epel-release package with the following command:

```
$ sudo yum install epel-release -y
```
Now you can install Ansible with the following command:
```
$ sudo yum install ansible -y
```
Now check that Ansible is installed with the following command:
```
$ ansible --version
```
As you can see from the screenshot below, the version of Ansible installed in my machine is 2.9.27

# Testing Ansible
To manage servers with Ansible, you first have to install SSH server software in the servers. This is the only requirement.
You can install SSH server software on Ubuntu or Debian server with the following commands:
```
$ sudo apt-get update
$ sudo apt-get install openssh-server -y
```

You can install SSH server on Red Hat Enterprise Linux or CentOS 7 with the following command:
```
$ sudo yum install openssh-server -y
```
Now I will check whether SSH server is running on target1 and target2
```
$ sudo systemctl status sshd
```

Now let’s check the IP address of target1 with the following command:
```
target1 $ ip a
```
As you can see from the marked section of the screenshot below, the IP address of target1 is 192.168.1.41

Now let’s check the IP address of target2 with the following command:
```
target2 $ ip a
```
As you can see from the marked section of the screenshot below, the IP address of target2 is 192.168.1.42

Now on your ansiblecontroller machine where you installed Ansible, run the following command to open /etc/ansible/hosts file:
```
$ sudo nano /etc/ansible/hosts
```
Now add the IP addresses or hostnames of the target servers that you want to manage with Ansible in that file.

## SSH Key Generation
> [!IMPORTANT]
> You still have to do one more thing before you can get started. That is, you have to copy a piece of SSH key of your CentOS 7 machine where you installed Ansible to the server that you want to manage. That way you won’t have to login to the target servers with password manually every time.

First generate a SSH key with the following command:
```
$ ssh-keygen
```
Now run the following commands to copy the ssh key:
```
$ ssh-copy-id student@target1
$ ssh-copy-id student@target2
```

Now you can ping the target servers to see whether they are online or not with the following command:
```
$ ansible -m ping all
```
```
[student@ansiblecontroller ~]$ ansible -m ping all
192.168.1.42 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
192.168.1.41 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

As you can see, the ping succeeded.

> [!NOTE]
> If you’re also using Ubuntu server for the demo, and the Ansible command fails, then you may try to install python2 on your target Ubuntu server with the following command:
```
$ sudo apt-get install python -y
```
