# kickup
Automatized installation of servers for gis4dis infrastructure.


## Operating system
Linux is highly recommended. This devstack is neither used nor tested under Windows.

If you are using Windows, consider installing Linux (e.g. Ubuntu) as dual boot. Disk volume with 50 -- 70 GB should be enough for Linux and this devstack.

## Requirements
- [vagrant 2.0.1+](https://www.vagrantup.com/docs/installation/)
- [virtualbox 5.2](https://www.virtualbox.org/wiki/Downloads)
   - Ubuntu
     - http://ubuntuhandbook.org/index.php/2017/10/virtualbox-reached-5-2-major-release-how-to-install/
     - Uninstall `virtualbox-dkms` before installation:
     ```
     sudo apt-get remove --auto-remove virtualbox-dkms
     ```
     - If you encounter message `Your system has UEFI Secure Boot enabled.`, [this manual](https://stegard.net/2016/10/virtualbox-secure-boot-ubuntu-fail/) can help you.
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

### Run `rsync` during development
```
cd gateway
vagrant rsync-auto
```

### Tunnel DB
You can use ssh tunnels to connect to remote db via ssh (Postgres SSH tunnel has some issues)
```
ssh -L 3333:localhost:5432 user@your.db.server
```
This will connect to ssh on your.db.server and then make tunnel from (server's)localhost:5432 to 3333 on (machine's)localhost. Resulting in having remote postgres "mounted" on your locahost:3333.

Now you can connect to localhost:3333

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
