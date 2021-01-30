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


* Populate Inventory and configuration
* Install Python
* Set up passwordless ssh key access
* Set up passwordless sudo

## Test
* Run test shell script

## Deploy LAMP installation
### Run the playbook to install LAMP
