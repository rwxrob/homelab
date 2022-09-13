# Ansible

I don't particularly like Ansible --- especially the drippy enterprise
marketing stank --- but it doesn't matter.

## Install

I prefer *not* to install `pip` if I can help it. (I detest 😈 Python
package management with a deep abiding hatred, and always have.)

```shell
#!/bin/sh
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
sudo apt install python3-argcomplete
sudo activate-global-python-argcomplete3
```

* Installing Ansible --- Ansible Documentation  
  https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

## Organizing Playbooks

> 🤮 God I hate the moniker "playbooks". Why the fuck bring American sports
metaphors into this?

I've decided to keep all my personal playbooks in my dot files next to
my install scripts and other stuff (for now). Playbooks that have
re-usable value will get their own repos.

* Setting Up And Executing Basic Ansible Playbook  
  https://www.c-sharpcorner.com/article/setting-up-and-executing-basic-ansible-playbook/

## Create Initial `hosts` File

At first will use the IPs of the targets in order to setup CoreDNS, but
after that will use only domain names to enable changes to IP addressing
without impact.

I put the following initially into `/etc/ansible/hosts`:

```
sudo mkdir -p /etc/ansible
```

Then add the following to `/etc/ansible/hosts`:

```
192.168.1.101
192.168.1.102
192.168.1.103
```

Test it out.

```
ansible all -m ping
```

```out
[WARNING]: Unable to parse /home/rwxrob/.config/ansible/hosts as an
inventory source
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that
the implicit localhost does not match 'all'
```

Turns out I forgot I added the following to my `.bashrc`:

```bash
export ANSIBLE_INVENTORY="$HOME/.config/ansible/hosts"
```

So I added a `dot/ansible` directory to my dot files and the following
`setup` script:

```bash
#!/usr/bin/env bash

## This assumes you have the following in your .bashrc:
##  export ANSIBLE_INVENTORY="$HOME/.config/ansible/hosts"

if ! command -v ansible; then
  echo "Ansible isn't installed. Skipping."
  exit 1
fi

mkdir -p $HOME/.config/ansible
ln -sf "$PWD/hosts" "$HOME/.config/ansible/hosts"
```

This creates a symbolic link.

```
ls -l ~/.config/ansible/hosts
```

```out
lrwxrwxrwx 1 rwxrob rwxrob 54 Sep 13 06:23 /home/rwxrob/.config/ansible/hosts -> /home/rwxrob/Repos/github.com/rwxrob/dot/ansible/hosts
```

Now we can try our command again.

```
ansible all -m ping
```

```out
192.168.1.101 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
192.168.1.103 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
192.168.1.102 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Then I learned about groups and added them all to a `control` group
since they will have CoreDNS, etcd, and K8S control plane installed.


```
[control]
192.168.1.101
192.168.1.102
192.168.1.103
```

```
ansible control -m ping
```

