# kickup
Automatized installation of servers for gis4dis infrastructure.

This repository uses vagrant for local development. So you need to install vagrant with a provider such as virtualbox.

See more at: https://www.vagrantup.com/docs/installation/

You also need to have Ansible installed on the host machine, see: http://docs.ansible.com/ansible/latest/intro_installation.html


## Installation

### git clone
If you have git 1.9+ you can use git clone with submodule initiation:

```
git clone --recursive https://github.com/gis4dis/kickup kickup
cd kickup
```

If you have already cloned repo or have older git, you need to init submodules separately like:

```
git clone https://github.com/gis4dis/kickup kickup
cd kickup
git submodule update --init --recursive
```

### vagrant up

Then you can just cd into folder you want to start and type `vagrant up`
That will kick up selected machine with provisioning run.

Machines currently are: gateway, processing

Eg.:
```
cd gateway
vagrant up
```

### instalation of ansible on ubuntu/xenial
```
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible
```

## Useful tips

### testing server inventory file
Edit `/etc/ansible/hosts` on target prod machine
```
[frontend]
<hostname>          ansible_connection=local

[testing]
<hostname>
```
### set group_vars for testing

### ansible playbook dry run
`ansible-playbook -C -D ./playbook.yml -v`
