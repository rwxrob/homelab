# Agent Context

## Conventions

- Use conventional commit messages (`feat:`, `fix:`, `docs:`, `refactor:`, `chore:`, etc.)
- Never add co-author attribution to commits
- Always commit and push to the `dev` branch — never commit directly to `main`
- Push immediately after every commit — no need to ask
- Do not use `-K` (become password prompt) with ansible-playbook — passwordless sudo is assumed on all nodes

## Cluster Overview

Four-node SLURM cluster running Rocky Linux 9 / RHEL 9:

- `slurmctl` (10.35.4.169) — SLURM controller (`slurmctld`)
- `slurm-c1` (10.35.4.173) — compute node (`slurmd`)
- `slurm-c2` (10.35.4.174) — compute node (`slurmd`)
- `slurm-c3` (10.35.4.175) — compute node (`slurmd`)

Each compute node has 2 CPUs. SLURM version 22.05.9 from EPEL on all nodes.

## Key Decisions

- Packages come from EPEL only — no OpenHPC
- SELinux is set to permissive (slurmscriptd crashes in enforcing mode)
- Firewall ports open on all nodes: 6817/tcp, 6818/tcp, 6819/tcp, 6820-6830/tcp
- `SrunPortRange=6820-6830` in slurm.conf — required for srun interactive output to work
- `ProctrackType=proctrack/cgroup` and `TaskPlugin=task/cgroup` — orphaned child processes are killed when a job ends
- `DependencyParameters=kill_invalid_depend` — jobs with unsatisfiable dependencies are killed automatically
- Munge key is backed up to `backups/munge.key` (gitignored) after deployment
- Node facts (CPU/RAM) are cached in `.ansible_facts_cache/` for 24h (jsonfile backend)
- SLURM accounting uses `slurmdbd` backed by MariaDB on `slurmctl`; `slurm-slurmdbd` from EPEL; DB credentials hardcoded in `/etc/slurm/slurmdbd.conf` (not vaulted)
- `slurmdbd.conf` must be `0600 slurm:slurm` or slurmdbd refuses to start
- `slurmdbd` must be running before `slurmctld` starts

## Playbook Order

`install-slurm` runs:
1. `playbooks/munge.yaml` — installs Munge, distributes shared key, validates token auth
2. `playbooks/slurm.yaml` — installs SLURM, generates and distributes `slurm.conf` from live facts, starts services, validates with a test job
3. `playbooks/accounting.yaml` — installs MariaDB and slurm-slurmdbd, configures DB, writes slurmdbd.conf, starts slurmdbd, registers cluster, validates with sacct

## Validation

The slurm.yaml playbook ends with:
- `scontrol ping` on the controller
- `sinfo` to check node states
- `srun --nodes=3 --ntasks-per-node=1 hostname` as a live test job
