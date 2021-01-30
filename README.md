# Install LAMP stack on servers using Ansible
Provision LAMP stack on multiple servers using Ansible Playbook

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
host-03
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
* ```ssh-keygen -b 2048 -f ~/.ssh/id_rsa -t rsa -q -N ""```

Now copy the contents of ~/.ssh/id_rsa.pub into our destination hosts ~/.ssh/authorized_keys.

Or alternatively, if you have password authentication enabled, you can use ```ssh-copy-id root@10.0.0.x``` etc.

Now we should be able to ssh to our nodes to host-01, host-02 etc.

### Install Python on servers
Now we need to install Python on destination hosts which can be achieved using below command.
* ```ansible -m raw -s -a "apt update && apt install python -y" -i inventory.ini hosts```

### Set up passwordless sudo
The account set up in config file and used for connectivity to hosts should have sudo access and should be set up with passwordless sudo. If the sudo commands ask for password for the user you can amend sudo configuration by running ```sudo visudo``` and modifying ```#%sudo	ALL=(ALL:ALL) ALL``` to ```%sudo  ALL=(ALL:ALL) NOPASSWD: ALL``` on all destination hosts.

## Test
The test script included has couple of commands including ```echo "hello world"``` and ```hostname``` defined. Run this to confirm your inventory and connectivity and investigate any errors.
```ansible-playbook -i inventory.ini shell-script-playbook.yml ```

## Deploy LAMP installation
You can run below command to execute playbook which will go and install LAMP stack on all servers within the inventory defined.
```ansible-playbook -i inventory.ini lamp.yml```
If your destination hosts are not set up for passwordless sudo you can use following to be able t enter password.
```ansible-playbook -i inventory.ini --ask-become-pass lamp.yml```
Running this playbook will install following packages on destination hosts.
- [x] apache2
- [x] mysql-server
- [x] php7.4
- [x] php7.4-mysql

It will also start apache2 and mysql and will also set them up to start on boot.

Finally, it will copy index.html to each destination host /var/www/html directory.
