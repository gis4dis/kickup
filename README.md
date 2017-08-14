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
```yaml
---
- name: "Create <{{ _uwsgi_app.name }}> user"
  user:
    name: "{{ _uwsgi_app.user }}"
    home: "{{ _uwsgi_app.app_root }}"

- set_fact:
    _uwsgi_virtualenv: '{{ _uwsgi_app.app_root }}/virtualenv'
    _uwsgi_activate: '{{ _uwsgi_app.app_root }}/virtualenv/bin/activate'

- name: Ensure directories have correct owner
  file:
    dest: "{{ _uwsgi_app.app_root }}"
    owner: "{{ _uwsgi_app.user }}"
    group: "{{ _uwsgi_app.user }}"
    recurse: "yes"

- name: "Create uwsgi.ini for <{{ _uwsgi_app.name }}>"
  template:
    src: uwsgi-ini.j2
    dest: '{{ _uwsgi_app.app_root }}/uwsgi.ini'
    owner: root

- name: "Create virtualenv for <{{ _uwsgi_app.name }}>"
  become_user: '{{ _uwsgi_app.user }}'
  shell: "virtualenv --python={{ _uwsgi_app.python_version_exec }} {{ _uwsgi_app.app_root }}/virtualenv"
  args:
    creates: '{{ _uwsgi_app.app_root }}/virtualenv/bin/python'

- name: "Create requirements.txt on <{{ _uwsgi_app.name }}>"
  copy:
    content: |
        # Managed by ansible. Please don't touch.
        {{ _uwsgi_app.requirements|default('') }}
    dest: "{{ _uwsgi_app.app_root }}/requirements-extra.txt"
    owner: '{{ _uwsgi_app.user }}'
    force: no  # don't overwrite
  register: _pip_requirements

- name: Create src dir
  file:
    path: '{{ _uwsgi_app.app_root }}/src'
    state: directory
    owner: '{{ _uwsgi_app.user }}'

- name: Upload wsgi entrypoint
  template:
    src: hello.py.j2
    dest: '{{ _uwsgi_app.app_root }}/src/wsgi.py'
    owner: '{{ _uwsgi_app.user }}'
    force: no   # don't overwrite

- name: "Compile requirements"
  shell: '{{ _uwsgi_virtualenv }}/bin/pip install -r requirements-extra.txt'
  args:
    chdir: '{{ _uwsgi_app.app_root }}'
  become_user: '{{ _uwsgi_app.user }}'
  when: _pip_requirements|changed

- name: Add virtualenv to login script
  become_user: '{{ _uwsgi_app.user }}'
  lineinfile:
    dest: '{{ _uwsgi_app.app_root }}/.bashrc'
    line: "if [ -f {{ _uwsgi_activate }} ]; then VIRTUAL_ENV_DISABLE_PROMPT=1; . {{ _uwsgi_activate }}; fi"
    state: present
    create: yes

- name: "Create uwsgi sockets in /run/<{{ _uwsgi_app.name }}>"
  template:
    src: uwsgi-tmpdir.j2
    dest: '/etc/tmpfiles.d/uwsgi-{{ _uwsgi_app.name }}.conf'
    owner: root
  notify: update tmpfiles

- name: "Create systemd units <{{ _uwsgi_app.name }}>"
  template:
    src: uwsgi-service.j2
    dest: '/etc/systemd/system/uwsgi-{{ _uwsgi_app.name }}.service'
    owner: root
  notify: reload systemd units
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
