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

### Get source code
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

### Edit `gateway/roles/verosk.uwsgi-app/tasks/one-app.yml`
According to https://github.com/VerosK/ansible-uwsgi-app/pull/2/files

### Configure poster

[Configure `local_settings.py`](https://github.com/gis4dis/poster/blob/master/README.md#3-configure-local_settingspy) in `poster/src/poster` folder.

### Run machines

Then you can just cd into folder you want to start and type `vagrant up`
That will kick up selected machine with provisioning run.

Current machines
- **gateway** (download and/or import data into DB)
- **processing** (process data)

Eg.:
```
cd gateway
vagrant up
```

### Open application
http://192.168.49.49/admin/


## Useful tips

### Make and sync migrations
Connect to vagrant:
```
cd gateway
vagrant ssh
```

Run under `vagrant` user:
```
source /opt/poster-app/virtualenv/bin/activate
cd /opt/poster-app/poster/src
python manage.py makemigrations
```

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
