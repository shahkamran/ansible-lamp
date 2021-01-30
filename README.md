# Install LAMP stack on servers using Ansible
Provision LAMP stack on multiple servers using Ansible Playbook

This Ansible playbook has been tested on destination hosts running following Linux distributions but can be easily adopted for other Linux distributions.

* Ubuntu 20.04.1 LTS
* CentOS Linux release 8.3.2011

Here is list of other package versions used in this repository/ playbook.

* ansible 2.10.5 (MacOS)
* PHP 7.4.3 (Ubuntu)
* PHP 7.2.24 (CentOS)
* Apache/2.4.41 (Ubuntu)
* Apache/2.4.37 (centos)
* MySQL 8.0.22-0ubuntu0.20.04.3 (Ubuntu)
* MySQL 8.0.21 (CentOS)

## Pre-requisites
### Install Ansible
Ansible heavily depends on Python and will require Python and associated dependencies. On your client you can run following commands to install dependencies.
```
apt update && apt install python python-setuptools -y
easy_install pip
pip install ansible
```
### Populate Inventory and configuration
Inventory initialisation file contains list of hosts and will require adding the list of host aliases in inventory.ini file.
```
# inventory.ini
[hosts]
host-01
host-02
```
All of the hosts will also require connection configuration defined in config file. The file example provided (hosts-ssh-config) will have to be moved to ~/.ssh/config with appropriate amendments.

```
# hosts-ssh-config - move to ~/.ssh/config
Host host-01
  Hostname 192.168.0.5
  User root
  IdentityFile ~/.ssh/id_rsa
  StrictHostKeyChecking no
  UserKnownHostsFile /dev/null
Host host-02
  Hostname 10.0.0.5
  User root
  IdentityFile ~/.ssh/id_rsa
  StrictHostKeyChecking no
  UserKnownHostsFile /dev/null
```

### Set up passwordless ssh key access
If you do not have an id_rsa key already set up or do not want to use it, create a new key using command below.

```ssh-keygen -b 2048 -f ~/.ssh/id_rsa -t rsa -q -N ""```

Now copy the contents of ~/.ssh/id_rsa.pub into our destination hosts ~/.ssh/authorized_keys.

Or alternatively, if you have password authentication enabled, you can use ```ssh-copy-id root@10.0.0.x``` etc.

Now we should be able to ssh to our nodes to host-01, host-02 etc.

### Install Python on servers
Now we need to install Python on destination hosts which can be achieved using below command. If you run yum on Ubuntu or apt on CentOS it will end in error and will not harm anything in my view but please run at your own risk. 

For Debian/Ubuntu hosts run following.

```ansible hosts -b -m raw  -a "apt update && apt install python3 -y" -i inventory.ini```

For RedHat/CentOS hosts run below command.

```ansible hosts -b -m raw -a "yum update -y&& yum install python3 -y" -i inventory.ini```

### Set up passwordless sudo
The account set up in config file and used for connectivity to hosts should have sudo access and should be set up with passwordless sudo. If the sudo commands ask for password for the user you can amend sudo configuration by running ```sudo visudo``` and modifying ```#%sudo	ALL=(ALL:ALL) ALL``` to ```%sudo  ALL=(ALL:ALL) NOPASSWD: ALL``` on all destination hosts.

## Test
The test script included has couple of commands including ```echo "hello world"``` and ```hostname``` defined. Run this to confirm your inventory and connectivity and investigate any errors.

```ansible-playbook -i inventory.ini test-shell-playbook.yml```

## Deploy LAMP installation
You can run below command to execute playbook which will go and install LAMP stack on all servers within the inventory defined.

```ansible-playbook -i inventory.ini lamp.yml```

If your destination hosts are not set up for passwordless sudo you can use following to be able t enter password.

```ansible-playbook -b -i inventory.ini lamp.yml```

Running this playbook will install following packages on destination hosts.
- [x] apache2
- [x] mysql-server
- [x] php7.4
- [x] php7.4-mysql

It will also start apache2 and mysql and will also set them up to start on boot.

Finally, it will copy index.html to each destination host /var/www/html directory.
