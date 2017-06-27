# kickup
Automatized installation of servers for gis4dis infrastructure.

This repository uses vagrant for local development. So you need to install vagrant with a provider such as virtualbox.

See more at: https://www.vagrantup.com/docs/installation/


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

Then you can just type `vagrant up` and it will kick up basic machine with
Nginx as webserver and uwsgi for backend server.

```
cd gateway
vagrant up
```

It will also prepare "dummy" wsgi application placeholders defined in `gateway/group_vars/frontend/app.yml` under `uwsgi_apps` key.

### instalation of ansible on ubuntu/xenial
```
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible
```

### testing server inventory file
```
[frontend]
<hostname>          ansible_connection=local

[testing]
<hostname>
```

### ansible playbook dry run
`ansible-playbook -C -D ./playbook.yml -v`
