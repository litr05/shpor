# Installing Ansible  

## In a virtual environment with pip

```bash
python -m virtualenv ansible
source ansible/bin/activate
python -m pip install ansible
```

## On Debian

- Add the following line to /etc/apt/sources.list or /etc/apt/sources.list.d/ansible.list:

```bash
deb http://ppa.launchpad.net/ansible/ansible/ubuntu MATCHING_UBUNTU_CODENAME_HERE main
```

- Example for Debian 11 (Bullseye)

```bash
deb http://ppa.launchpad.net/ansible/ansible/ubuntu focal main
```

- Then run these commands:

```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367
sudo apt update
sudo apt install ansible
```
