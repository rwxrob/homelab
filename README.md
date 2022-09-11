# 🏡💻 Enterprise Home Lab

This repo contains the design, details, and planning involved in
creating my home lab suitable for enterprise-level work. I've opted to
publish these details in the hopes is helps others learn and be inspired
to create their own.

> ⚡ Most people should probably not publish this kind of detail for
> hackers to use against them. I have a special case because I've
> already chosen to be so public about everything I do on Twitch and
> YouTube for the sake of education. Others will probably not want to
> take such risks.

## Hardware Summary

I have one VM server host and a bunch of hardware left over from
[SKILSTAK](https://skilstak.io). I've also added some hardware for
machine learning (eventually). This does not include my gaming/streaming
rigs, laptops, or any other devices in our home.

* 1 x [HP Z640](https://a.co/d/2QieEnW) (28 cores, 128 GiB RAM, 512 GiB SSD)
* 20 x 2016 Mac Mini (4 cores, 4 GiB RAM, 512 GiB HD)
* 6 x MSI Trident (4 cores, 8 GiB RAM, 1 TiB HD)
* 1 x Mac Pro
* 1 x iMac
* 1 x Raspi 4
* 2 x Raspi 3B
* 2 x nVidia Jetson Nano (2 GiB version)
* 1 x Netgear Nighthawk X6 R8000 (with USB SMB-accessible storage)
* 1 x [Netgear 24-Port (12 PoE) GiB Switch](https://a.co/d/5uAJry2)
* 1 x [TP-Link 24 Port GiB Switch](https://a.co/d/iq1AupR)

(I donated more than a dozen Raspi 2s to friends on stream for projects.)

## Planned Use Cases

The purpose of this home lab is to facilitate development of on-prem
cloud native infrastructure management software and services primarily
involving Kubernetes and major Kubernetes applications.

* Install and maintain a primary Kubernetes cluster on-prem
* Experiment with different separate K8S clusters
* Explore Microk8s and k3s and Talos on Raspberry Pi
* Experiment with communication between clusters (mTLS+gRPC, REST)
* Practice SMB, NFS, and Cephfs K8S storage class providers
* Practice identity management with Keycloak, Kerberos, OpenLDAP
* Practice configuration management with enterprise tools (Ansible)
* Develop infrastructure management software and APIs
* Develop distributed rootkits, botnets, malware targeting cloud
* Automate bug bounty discovery and stealth target scanning
* Practice intrusion detection and develop software to assist

## Main Kubernetes Stack

* Ubuntu Servers
* Ansible
* CRI-O
* etcd
* Calico
* MetalLB
* NFS Storage Class
* SMB Storage Class
* CephFS Storage Class
* Sidero/Talos
* Keycloak
* OPA Gatekeeper
* Harbor
* Istio
* Tekton
* Argo
* Elastic Search
* Prometheus
* Thanos
* Calico
* Graphana

## Design Decisions

**Ubuntu Server.** Red Hat might be big in enterprise, but unnecessary
in my home lab. Ubuntu Server skills are far more important for
containers and developers to master.

**No FreeIPA.** Primarily a Red Hat technology, can be used on Ubuntu
but swimming upstream to do so. Also, Keycloak is mandatory for things
that will mirror what I do at work.

*No Proxmox.* Only supports 32 nodes per cluster and falls on it's face
with just 14 (which I tried). Using KVM directly and Virtual Machine
Manager instead, which saves on a lot of Proxmox framework overhead
waste as well.

*No VMs for main Kubernetes.* One distinct advantage of on-prem
Kubernetes is that the 30% performance penalty from virtualisation
(which all cloud providers have) can be avoided.

*etcd not in pod.* Even though `kubeadm` will nicely setup etcd within a
Pod *inside* Kubernetes, this makes me uncomfortable. If K8S dies,
everything dies. So having K8S problems could actually prevent me from
keeping etcd healthy. Having etcd external will require slightly more
management (independent certificate management cycles, out-of-cluster
backup methods, etc.) but I sleep better knowing the most important
component of my K8S cluster has its own home.

*192.168.1.0/24 subnet.* This is the default in my home router. It has
plenty of IP addresses for the devices on our home network and I don't
feel like changing that right now. Most new machines will be VMs on a
private virtual network within Kubernetes. Rather than use network
segmentation for protection, I use a zero-trust approach to not trust
any device on the network and gather and audit all access with
centralized logging.

*Physical key login.* All access is simplified and hardened by the use
of a Yubikey physical key. This includes SSH and
Keycloak/OpenLDAP/Kerberos. The goal is to never allow any access to any
device from any user whatsoever without possessing one. Even if someone
did get in and elevate permissions while not being caught by real-time
auditing they still wouldn't be able to use any of the certs for that
user without being able to touch the physical key.

*No disk encryption.* I just don't need it so the overhead would be
wasted. Besides, if I did need it, I could just encrypt a loopback
mounted storage block file or just use GPG.

## Stages of Personal Home Lab Evolution

Here are the stages of my own personal home lab over a few decades.
I'm at stage #7 now.

1.  Personal laptop and ssh access to cloud servers
2.  Built my own on-prem (garage) Linux server machine from scratch
3.  Added old discarded machines from friends and family with Linux
4.  Used VMware Player/Workstation and 1-3 headless VMs on “desktop”
5.  Acquired several Mac Minis and Raspi while at SKILSTAK
6.  Added dedicated Type 1 Hypervisor server, K8S, plug and play
7.  Add Enterprise rack and multiple components
8.  Configured multiple secure virtual and physical networks

## Devices (Eventually)

Rack 1 (21U)

* ON: Monitor, Keyboard, Mouse, Netgear Wifi Router
* 2U: ISP Cable Modem and Power, Misc.
* 4U: [HP Z640 Mid-Tower Workstation](https://a.co/d/2QieEnW) (28/128)
* 1U: TP-Link 24-Port Switch with VLAN
* 1U: 24-Port Patch Panel
* 1U: PDU 14+2 Outlet (w/display)
* 5U: 10 Mac Minis (shelf)
* 1U: PDU 14+2 Outlet (w/display)
* 5U: 10 Mac Minis (shelf)

Rack 2 (21U)

* 4U: USB Power Supply, Nanos, Pis (shelf)
* 1U: [NETGEAR 24-Port PoE](https://a.co/d/irM1DOg)
* 1U: 24-Port Patch Panel
* 1U: PDU 14+2 Outlet (w/display)
* 2U: MSI Trident (shelf)
* 2U: MSI Trident (shelf)
* 2U: MSI Trident (shelf)
* 2U: MSI Trident (shelf)
* 2U: MSI Trident (shelf)
* 2U: MSI Trident, Mac Pro (bottom)

## Related:

* [Homelabbity](https://www.reddit.com/r/homelab/)
* [LabPorn](https://www.reddit/r/LabPorn/)
