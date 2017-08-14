# kickup
Automatized installation of servers for gis4dis infrastructure.


## Requirements
- [vagrant](https://www.vagrantup.com/docs/installation/)
   - Ubuntu: Version 1.9.7 had a problem with running `rsync-auto` that is almost necessary for development. Version 1.9.5 seems to work correctly.
- [virtualbox](https://www.virtualbox.org/wiki/Downloads)
   - Ubuntu: `sudo apt-get install virtualbox`. If you encounter message `Your system has UEFI Secure Boot enabled.`, [this manual](https://stegard.net/2016/10/virtualbox-secure-boot-ubuntu-fail/) can help you.
- [ansible](http://docs.ansible.com/ansible/latest/intro_installation.html)

### Recommended
- [git](https://git-scm.com/downloads) + account at [github](https://github.com/join)


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
