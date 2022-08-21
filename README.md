# 🏡💻 Personal Home Lab

This repo contains the design, details, and planning your first personal
home lab that can be used for experimentation and learning for work or
just personal growth. It includes recommended system specs for common
rig that should work for most people with the follFuquay-Varinaowing needs and
requirements:

* Designed primarily for IT infrastructure engineers
* Must work in a studio (New York style) apartment
* Must be completely portable
* Must cost under \$2500 USD
* Used as headless, dedicated, separate server
* Provides maximum employment potential (not hobbyists)
* Requires at least one GPU for ML job projects
* Minimum of 12 cores and 128 GiB RAM

Sample systems that fit this description:

* [HP Z640 Mid-Tower Workstation](https://a.co/d/2QieEnW)
* [HP Z640 Specs](https://zworkstations.com/products/z640/)

## Stages of Personal Home Lab Evolution

0. Personal laptop and ssh access to cloud servers
1. Add VMware Player/Workstation and 1-3 headless VMs
2. Add dedicated Type 1 Hypervisor server, Proxmox, K8S, plug and play
3. Add network attached storage device + IAM + UPS
4. Enterprise rack + switch + multiple components, ESXi + own 20-amp circuit(s)
5. Multiple secure VLANs and physical networks
6. TFTP and PXEBOOT for multiple hardware instances

## Kubernetes Images


## Devices

* [NETGEAR 24-Port PoE](https://a.co/d/irM1DOg)
* 14 Mac Minis

6U Shelf
* 4 MSI Tridents

2U Rack
* 16 Raspi 3/4


## Related:

* [Homelabbity](https://www.reddit.com/r/homelab/)
* [LabPorn](https://www.reddit/r/LabPorn/)

## Questions:

*Why not Raspberry Pis / SBC?*

*Woah, why so expensive?* This isn't expensive at all. Most good gaming
rigs cost more. Plus this is an essential tool to progressing in the IT
engineering field. Without it learning and experimenting is much harder.

*Can't I do this on a laptop?* No.

*Why ESXi and not Proxmox or XCP-ng?* ESXi is enterprise de facto
standard and pays the most for skills using it.

*Why VMware and not libvirt/KVM?*
