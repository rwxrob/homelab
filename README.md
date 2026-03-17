# homelab

Ansible playbooks for a SLURM compute cluster.

## Requirements

- Ansible on the machine you run playbooks from
- Fedora/RHEL-based nodes reachable via SSH
- Same username on all nodes with passwordless sudo
- Python 3 at `/usr/bin/python3` on all nodes

## Inventory

Edit `inventory.yaml` to set your hostnames:

- `slurmctl` — SLURM controller node
- `slurm-c1`, `slurm-c2`, `slurm-c3` — compute nodes

Username is omitted intentionally — Ansible uses your current active user and assumes it exists on all nodes with sudo.

## Usage

```sh
./install-slurm
```

This runs the following playbooks in order:

1. `playbooks/munge.yaml` — installs Munge, distributes a shared key, starts the service, and validates token auth on every node
2. `playbooks/slurm.yaml` — installs SLURM, generates `slurm.conf` from live node facts, distributes it, starts services, and validates the cluster with a test job
3. `playbooks/accounting.yaml` — installs MariaDB and slurm-slurmdbd on the controller, configures the database, starts slurmdbd, and validates accounting with sacct

## Session logging

To capture a terminal session log:

```sh
script session.log
```

`session.log` is gitignored. Agents do not tail this file automatically — ask explicitly if you want live log monitoring.

## Munge key backup

After deployment the munge key is fetched from the controller to `backups/munge.key` on the local machine. Keep this somewhere safe — it is the only way to restore Munge authentication if the controller is lost. The `backups/` directory is gitignored.

## Fact caching

Node facts (CPU count, RAM) are cached locally in `.ansible_facts_cache/` for 24 hours so repeated runs skip the fact-gathering phase. To force a refresh:

```sh
ansible-playbook playbooks/slurm.yaml --flush-cache
```

## Accounting

Job accounting is stored in MariaDB on the controller via `slurmdbd`. The database credentials are hardcoded in `/etc/slurm/slurmdbd.conf` on `slurmctl`. To rerun accounting setup independently:

```sh
ansible-playbook playbooks/accounting.yaml
```

## Rerunning individual playbooks

```sh
ansible-playbook playbooks/munge.yaml
ansible-playbook playbooks/slurm.yaml
ansible-playbook playbooks/accounting.yaml
```
