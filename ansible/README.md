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

I've decided to keep some of my personal playbooks in my dot files next to
my install scripts and other stuff, but this directory contains stuff
for setting up and managing my homelab. Playbooks that have re-usable
value will eventually get their own repos.

I've decide to always use `-i inventory` just to be explicit about it.

* Setting Up And Executing Basic Ansible Playbook  
  https://www.c-sharpcorner.com/article/setting-up-and-executing-basic-ansible-playbook/

