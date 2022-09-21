# 🐧Linux 🏡 Homelab init

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
* 1 x ISP cable modem

(I donated more than a dozen Raspi 2s to friends on stream for projects.)

Eventually, I need to get a beefy, separate (not rack mounted) UPS and
possibly an iSCSI NAS device. The UPS should prevent the need for
dedicated 20Amp circuits.

## Planned Use Cases

The purpose of this apartment lab is to facilitate development of
on-prem cloud native infrastructure management software and services
primarily involving Kubernetes and major Kubernetes applications and
running pentesting scans intrusion detection experiments (honeypots).

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

## Infrastructure Stack

* Ubuntu Server OS
* Sidero/Talos OS
* CoreDNS Server
* Ansible
* Icinga2
* etcd
* Kubernetes
* CRI-O
* Calico
* MetalLB
* Node Feature Discovery (NFD)
* nVidia GPU Feature Discovery
* NFS Storage Class
* SMB Storage Class
* CephFS Storage Class
* Keycloak
* OpenLDAP/Kerberos
* OPA Gatekeeper
* Harbor/Trivy
* Istio
* Tekton
* ArgoCD
* Elastic Search
* LogStash (or Fluentd?)
* Kibana
* Seldon
* Prometheus
* Thanos
* Graphana
* Github Enterprise (\$245/year/person)

Other stuff I still have to learn:

* Airflow
* VertexAI
* GCP

## Design Decisions

**Ubuntu Server.** Red Hat might be big in enterprise, but unnecessary
in my home lab. Ubuntu Server skills are far more important for
containers and developers to master.

**No FreeIPA.** Primarily a Red Hat technology, can be used on Ubuntu
but swimming upstream to do so. Also, Keycloak is mandatory for things
that will mirror what I do at work.

**No Proxmox.** Only supports 32 nodes per cluster and falls on it's face
with just 14 (which I tried). Using KVM directly and Virtual Machine
Manager instead, which saves on a lot of Proxmox framework overhead
waste as well.

**No VMs for main Kubernetes.** One distinct advantage of on-prem
Kubernetes is that the 30% performance penalty from virtualisation
(which all cloud providers have) can be avoided.

**etcd outside of Kubernetes.** Even though `kubeadm` will nicely setup
etcd within a Pod *inside* Kubernetes, this makes me uncomfortable. If
K8S dies, everything dies. So having K8S problems could actually prevent
me from keeping etcd healthy. Having etcd external will require slightly
more management (independent certificate management cycles,
out-of-cluster backup methods, etc.) but I sleep better knowing the most
important component of my K8S cluster has its own home.

**192.168.1.0/24 subnet.** This is the default in my home router. It has
plenty of IP addresses for the devices on our home network and I don't
feel like changing that right now. Most new machines will be VMs on a
private virtual network within Kubernetes. Rather than use network
segmentation for protection, I use a zero-trust approach to not trust
any device on the network and gather and audit all access with
centralized logging. Also, should be using DNS for most things.

**Physical key login.** All access is simplified and hardened by the use
of a Yubikey physical key. This includes SSH and
Keycloak/OpenLDAP/Kerberos. The goal is to never allow any access to any
device from any user whatsoever without possessing one. Even if someone
did get in and elevate permissions while not being caught by real-time
auditing they still wouldn't be able to use any of the certs for that
user without being able to touch the physical key.

**No disk encryption.** I just don't need it so the overhead would be
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

## Rack Organization

I'm separating the hardware into two racks to make it easier to move
around and, when necessary, locate close to separate circuits (although
I'm shameless enough to run a power extension chord from a good 20A
bathroom circuit breaker if I need).

The 21U rack height allows me to put a monitor/keyboard/mouse on top and
have it be the perfect height to use while standing. Since only the HP
server is fixed (and on rails), I can pull anything out on the rare
occasion when I need to plug them into a monitor, etc.

### Rack 1 (21U)

* ON: Dell Monitor, keyboard, mouse
* 1U: 20A (2400W) PDU 10+2 Outlet (w/display)
* 1U: TP-Link 24-Port Switch with VLAN (14W to PDU)
* 1U: 24-Port Cat6 Keystone Patch Panel
* 2U: MSI Trident (shelf) (230W to PDU)
* 2U: MSI Trident (shelf) (230W to PDU)
* 2U: MSI Trident (shelf) (230W to PDU)
* 2U: MSI Trident (shelf) (230W to PDU)
* 2U: MSI Trident (shelf) (230W to PDU)
* 2U: MSI Trident (shelf) (230W to PDU)
* 2U: UPS (w/display)
* 4U: HP Z640 Mid-Tower Workstation (w/rails) (975W to PDU)

Max Power Consumption:

Devices|Watts
-|-
Switch      |   14W
HP Server   |  975W
MSI Tridents| 1380W
Dell Monitor|   33W
TOTAL       | 2402W

### Rack 2 (21U)

* ON: Netgear Wifi Router (5W to Mixed Power)
* 1U: 15A (1800W) PDU 12+2 Outlet (w/display)
* 1U: 15A (1800W) PDU 12+2 Outlet (w/display)
* 1U: NETGEAR 24-Port (12 PoE, 100W to PDU1)
* 1U: 24-Port Cat6 Keystone Patch Panel
* 3U: ISP Modem (5W to Mixed Power)
* xx: Mixed Power strip (to PDU1)
* 2U: 2 x Jeston Nanos, Pi4, 2 x Pi3 (self,5x5W to Mixed Power)
* 5U: 10 Mac Minis (shelf) (10x85W to PDU1)
* 5U: 10 Mac Minis (shelf) (10x85W to PDU2)
* xx: Mac Pro (shelf behind minis) (902W to PDU2)
* 2U: 2700W(3000VA) CyberPower OL3000RTXL2U UPS (w/display)

Max Power Consumption:

Devices|Watts
-|-
Switch      |   100W
Mixed Power |    35W
Minis       |  1700W
Mac Pro     |   902W
TOTAL       |  2737W

## Related:

* [Homelabbity](https://www.reddit.com/r/homelab/)
* [LabPorn](https://www.reddit/r/LabPorn/)
