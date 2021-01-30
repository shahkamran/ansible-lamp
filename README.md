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
Host host01
  Hostname 192.168.0.5
  User root
  IdentityFile ~/.ssh/id_rsa
  StrictHostKeyChecking no
  UserKnownHostsFile /dev/null
Host host02
  Hostname 10.0.0.5
  User root
  IdentityFile ~/.ssh/id_rsa
  StrictHostKeyChecking no
  UserKnownHostsFile /dev/null
```

* Install Python
* Set up passwordless ssh key access
* Set up passwordless sudo

## Test
* Run test shell script

## Deploy LAMP installation
### Run the playbook to install LAMP
