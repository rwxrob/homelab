# Ansible

I didn't particularly like Ansible when I first heard about it and saw a
lot of really bad python --- especially seeing all the drippy enterprise
marketing stank from Red Hat --- but it doesn't matter. I'm now a huge
Ansible fanboy even though I have a lot to learn about it still.

## Install

```shell
#!/bin/sh
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
sudo apt install python3-argcomplete
sudo activate-global-python-argcomplete3
```

* Installing Ansible --- Ansible Documentation  
  https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

To add tab completion add the following to your `~/.bashrc` (or
whatever):

```
_have() { type "$1" &>/dev/null; }
_have ansible && . <(register-python-argcomplete3 ansible)
_have ansible-config && . <(register-python-argcomplete3 ansible-config)
_have ansible-console && . <(register-python-argcomplete3 ansible-console)
_have ansible-doc && . <(register-python-argcomplete3 ansible-doc)
_have ansible-galaxy && . <(register-python-argcomplete3 ansible-galaxy)
_have ansible-inventory && . <(register-python-argcomplete3 ansible-inventory)
_have ansible-playbook && . <(register-python-argcomplete3 ansible-playbook)
_have ansible-pull && . <(register-python-argcomplete3 ansible-pull)
_have ansible-vault && . <(register-python-argcomplete3 ansible-vault)
```

## Organizing Playbooks

> 🤮 God I hate the moniker "playbooks". Why bring American sports
metaphors into this?

I've decided to keep some of my personal playbooks in my dot files next to
my install scripts and other stuff, but this directory contains stuff
for setting up and managing my homelab. Playbooks that have re-usable
value will eventually get their own repos.

I've decide to always use `-i inventory` just to be explicit about it.

* Setting Up And Executing Basic Ansible Playbook  
  https://www.c-sharpcorner.com/article/setting-up-and-executing-basic-ansible-playbook/

