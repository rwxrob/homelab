# 🏡💻 Personal/Professional Enterprise Home Lab

This repo contains the design, details, and planning involved in
creating my home lab dedicated to emulating my professional working
environment for training as well as for fun and profit doing other
things at home.

This home lab is designed to maximize experience and learning for
on-prem Kubernetes deployments and management and Kubernetes
applications development and delivery with a bias for systems utility
services, blue team programming, and inter-cluster operations. I want to
write a lot of Kubernetes operations applications including those that
communicate between clusters.

[TODO List](todo.md)

## Requirements and Target Specs

* Designed primarily for IT infrastructure engineers (and hackers)
* Must work in a studio (New York style) apartment closet
* Must be completely portable
* Must cost under \$5000 USD
* Should reuse existing hardware as much as possible (minis, pis)
* Must have a main headless, dedicated, separate server
* Requires at least one GPU for ML job projects
* Minimum scale: 32 VMs @ 2 cores, 2 GiB RAM, 40 GiB disk
* Must have DMZ separate from home network for pentest scanning

## Planned Use Cases

The purpose of this home lab is to facilitate development of on-prem
cloud native infrastructure management software and services primarily
involving Kubernetes and major Kubernetes applications.

* Install and maintain a primary Kubernetes cluster on-prem
* Experiment with different separate K8S clusters
* Explore Microk8s and k3s on Raspberry Pi
* Experiment with communication between clusters
* Practice use NFS and Cephfs K8S storage class providers
* Practice identity management with Keycloak, Kerberos, OpenLDAP
* Deploy and test Calico, MetalLB, Harbor, Istio, Tekton, Argo, Elastic
  Search, Prometheus, Thanos, Calico

## Design Decisions

**Ubuntu Server.** Red Hat might be big in enterprise, but unnecessary
in my home lab. Ubuntu Server skills are far more important for
containers and developers to master.

**No FreeIPA.** Primarily a Red Hat technology, can be used on Ubuntu
but swimming upstream to do so. Also, Keycloak is mandatory for things
that will mirror what I do at work.

*No Proxmox.* Only supports 32 nodes per cluster and falls on it's face
with just 14 (which I tried).

*No VMs for main Kubernetes.* One distinct advantage of on-prem
Kubernetes is that the 30% performance penalty from virtualisation
(which all cloud providers have) can be avoided.

*etcd not in pod.* Even though etcd will run on the same three control
plane nodes, it will not run from within a k8s pod as `kubeadm` does by
default. This will require independent certificate management cycles and
out-of-cluster backup methods.

## Stages of Personal Home Lab Evolution

Here are the stages of my own personal home lab. I'm at stage #7 now.

1.  Personal laptop and ssh access to cloud servers
2.  Built my own on-prem (garage) Linux server machine from scratch
3.  Added old discarded machines from friends and family with Linux
4.  Used VMware Player/Workstation and 1-3 headless VMs on “desktop”
5.  Acquired several Mac Minis and Raspi while at SKILSTAK
6.  Added dedicated Type 1 Hypervisor server, K8S, plug and play
7.  Add Enterprise 21U rack PDU + PoE switch(es) + multiple components
8.  Configured multiple secure virtual and physical networks

## Devices (Eventually)

* 4U: [HP Z640 Mid-Tower Workstation](https://a.co/d/2QieEnW) (14/128)
* 6U: 19 Mac Minis
* 2U: 16 Raspi (2/2 each)
* 2U: NAS Device (eventually)
* 1U: NETGEAR 16-Port PoE
* 1U: [NETGEAR 24-Port PoE](https://a.co/d/irM1DOg)
* 1U: PDU 14+2 Outlet (w/display)
* 1U: PDU 14+2 Outlet (w/display)
* 2U: [APC 2x20Amp Groups UPS](https://a.co/d/5LKX3LZ)

## Software and Services Deployment

* Where to put `etcd`?
* CephFS 5-6 MSI Tridents

## Related:

* [Homelabbity](https://www.reddit.com/r/homelab/)
* [LabPorn](https://www.reddit/r/LabPorn/)
