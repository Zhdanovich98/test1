## Steps to reproduce  ##

## Installation steps: ##

1. Install the correct python version on you local system:

```sh
apt install python3.9
```
> You can find the version you need with:

```sh
apt-cache search python | grep <version>
```
2. Install [virtualenv](https://virtualenv.pypa.io/en/latest/):
```sh
sudo pip3 install virtualenv==20.0.17
```
3. Install VirtualBox
```sh
 apt install virtualbox
 apt install virtualbox—ext–pack
```
> [More information about install VirtualBox](https://phoenixnap.com/kb/install-virtualbox-on-ubuntu)

4. Install Vagrant:
```sh
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install vagrant
```
> [See documentation](https://www.vagrantup.com/downloads)
___
## Steps to start: ##

1. Clone this project on you local system:
```sh
git clone https://github.com/Zhdanovich98/devsecops.git
```
2. Go to this project directory:
```sh
cd ./devsecops
```
3. Create a virtual environment:
```sh
virtualenv -p /usr/bin/python3.9 ./env
```
> /usr/bin/python3.9 - your local pass to python

> ./env - folder name with a virtual environment

> [more information about create/active a virtual environment](https://gist.github.com/frfahim/73c0fad6350332cef7a653bcd762f08d)

> [good article about venv, pyvenv, etc](https://stackoverflow.com/questions/41573587/what-is-the-difference-between-venv-pyvenv-pyenv-virtualenv-virtualenvwrappe)

4. Active your virtual environment:
```sh
source ./env/bin/activate
```
5. Install the correct(2.9.6) ansible version using requirements files:

```sh
pip install -r ./files/requirements.txt #install only ansible
```
>[Installing packages using pip and virtual environments](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/)

6. Create a virtual machine using Vagrant:

```sh
vagrant up
```

##### Start provision while the virtual machine is running: #####

```sh
vagrant provision
```

7. Check result:

```sh
vagrant ssh ntp.edu.tentixo.com #connect to ssh
```
```sh
chrony tracking #see information about chrony service
```
```sh
cat /etc/chrony.conf #see configuration file
```
```sh
exit #exit from the Virtual Machine
```
[More information about vagrant and it commands](https://www.vagrantup.com/docs)

8. To deactivate virtual environment:
```sh
deactivate
```
___
## Provisioning without vagrant: ##

1. Go to folder with playbook file:
```sh
 cd ./provision
```
2. Start playbook with inventory file:
```sh
ansible-playbook --inventory-file=../files/ansible/inventory  playbook.yml
```

> This only works when the virtual machine is running.

> This inventory file uses: ansible_host, ansible_user, ansible_ssh_port, ansible_ssh_private_key_file. The private key is created by vagrant and then ansible is used. Use this command for get pass to ssh private key:

```sh
vagrant ssh-config
```

[More information about Ansible Inventory](https://docs.ansible.com/ansible/2.3/intro_inventory.html)
___
## About Ansible roles in this project: ##

Main.yml file from change_ntp role:

```sh
---
#First update file
- name: First update line
  ansible.builtin.lineinfile:
    path: /etc/chrony.conf
    regexp: '^pool 2\.rhel\.pool\.ntp\.org'
    line: "pool\nserver"
    backup: yes
    backrefs: yes

#change ntp to ntp.se in pool line
- name: Change pool
  ansible.builtin.lineinfile:
    path: /etc/chrony.conf
    regexp: '^pool'
    line: "pool ntp.se"
    backrefs: yes

#change ntp to ntp.se in server line
- name: Change server
  ansible.builtin.lineinfile:
    path: /etc/chrony.conf
    regexp: '^server'
    line: "server 0.se.pool.ntp.org"
    backrefs: yes

#restart service
- name: Restart service chrony
  ansible.builtin.service:
    name: chronyd.service
    state: restarted
```

> "First update line" use [ansible.builtin.lineinfile](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html) module, which creates from one old line "pool 2.rhel.pool.ntp.org iburst
"  in [/etc/chrony.conf file](https://chrony.tuxfamily.org/doc/3.4/chrony.conf.html) two lines: "pool" and "server". This part work only one time.

> "Change pool" use [ansible.builtin.lineinfile](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html) module, which change one line "pool" to "pool ntp.se" in [/etc/chrony.conf file](https://chrony.tuxfamily.org/doc/3.4/chrony.conf.html). This line points to the region. In this line we change region.

> "Change server" use [ansible.builtin.lineinfile](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html) module, which change one line "server" to "server 0.se.pool.ntp.org" in [/etc/chrony.conf file](https://chrony.tuxfamily.org/doc/3.4/chrony.conf.html). This line points to the region. In this line we change region.

> "Restart service chrony" use [ansible.builtin.service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html) module, which restart chronyd.service after change in configuration file.
