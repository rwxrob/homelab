# Log of Work to Setup Homelab

## Friday, October 7, 2022, 10:50:23AM EDT

* Create control nodes using `kubeadm --control-plane-endpoint k8s:6443`
* Quick review of resources available during cert exam
* Determined CNCF certification is a fucking joke (actively against)
* Decided (and confirmed with hiring people) actual experience preferred
* Learned some control plane pods are ***static*** pods:
  `controller-manager`, `scheduler`, `kube-apiserver` as well as `etcd`
  (by default)
* CoreDNS runs as two pods on initial control-plane node (by default)
* Decided on Calico for CNI (will install later)
* Decided to use standard, "stacked", internal `etcd` (not external)
* Rethinking decision to use `kube-vip` vs `haproxy`/`keepalived`
* `kube-vip` is untestable until `kubelet` is out of crash loop
* `haproxy` is a systemd daemon that is immediately testable, but separate
* Proceeding with `kube-vip` as static pod for now, but might change
* I prefer having `haproxy` (deployed with Ansible) separate, reusable
* For some reason, I stopped in CRI setup before "Install and config prereqs"

Related:

* https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/
* https://github.com/kubernetes/kubeadm/blob/main/docs/ha-considerations.md
* https://github.com/kubernetes/kubeadm/issues/1685
* https://github.com/kubernetes/kubeadm/issues/1931

## Friday, October 6, 2022, 9:00:15PM EDT

* Decided to go with `kube-vip` since Tanzu decided to replace HA Proxy with it
* Struggled with realization that I need an "admin" cluster
* Reminded not to put container registry (Harbor) in "prod" cluster
* Revisit container runtimes in order to pull `kube-vip` static pod image
* Learned that ghcr.io is GitHub's container registry
* Installed `kube-vip` container from ghcr.io
* Decided that `crictl` is not quite enough, need to install `nerdctl`/`podman`
* Installed `podman` and found it much more "docker"-like

Related:

* https://kube-vip.io/docs/installation/static/
* https://github.com/kubernetes/kubeadm/blob/main/docs/ha-considerations.md

## Thursday, October 6, 2022, 11:58:22AM EDT

This session is largely about providing a single problematic argument to
`kubeadm` when setting up control planes with "high availability":
`--control-plane-endpoint`. Ultimately, I decided to go with a virtual
IP associated with a single specific hostname that is managed by
`kube-vip` and ARP elections as a static pod.

* Add `apt mark` to `kubeadm`, `kubelet`, `kubectl`
* Checked that `kubelet` is indeed crash-looping (as expected) 
* Reverted to 1.24 for everything (was 1.25)
* Configure the cgroup driver
* Decided that CRI-O defaults for cgroup systemd are good
* Rabbit hole: ClusterAPI/`clusterctl` instead of `kubeadm` (eventually)
* "Static" Pods are pods that are not defined by API, but in local YAML
* Explorations of LB, HA and DNS round-robin, and `kube-vip`

## Wednesday, October 5, 2022, 7:55:56PM EDT

* Install `kubeadm`, `kubelet`, `kubectl` with Ansible (from Internet)
* Decided not to explicitly block cluster from Internet access
* Planning on using k8s cluster for 24x7 pentest scanning
* Still, will "imaging" enterprise and use `deb`s for install
* Rule: nothing install on hosts directly from Internet
* Decided to be silly and create an Ubuntu internal mirror
* Discovered that mini1 and mini2 are LVM, so will use for mirrors
* Created group "repos" for mini1 and mini2
* Created a Ubuntu Server (Jammy) mirror and partition for it
* Decided to skip the nginx setup for repo mirror (for now)
* Decided to pretend "consulting" for small/mid-size company
* Decided on pretend scenario: academic think tank doing machine learning
* Such an organization is going to want unfettered access to Internet
* Opportunity to practice zero-trust at smaller scale
* Secure workstations and laptops without blocking outbound access
* Decided to seek better understanding of ZT at smaller scale
* Successfully installed `kubeadm`, `kubelet`, `kubectl`

## Tuesday, October 4, 2022, 10:03:08AM EDT

* Wrote ansible playbook to push container debs and install

## Saturday, October 1, 2022, 10:07:17AM EDT

* Figured out how to force JSON Ansible output
* Reorganized this homelab directory
* Added `setup` scripts for `hosts` and `~/.config/ansible`
* Streamlined overall Ansible setup (still need playbooks later)
* Reviewed container runtimes and CRI
* Discussed the problems with Docker for Kubernetes
* Decided to go with CRI-O
* Learned that Kubernetes and CRI-O versions are synced (1.24 -> 1.24)
* Decided to maintain local `.deb` packages as if pulling into enterprise
* Learned cri-tools contains crictl
* Learned conmon, containers-common are used by cri-o and cri-o-runc
* Added pull-debs script (and `*.debs`) to .gitignore

Related:

* https://docs.ansible.com/ansible/latest/user_guide/intro_patterns.html#patterns-and-ad-hoc-commands
* Container Runtimes \| Kubernetes Guide and Tutorial  
  https://www.containiq.com/post/container-runtimes
* https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/1.25/xUbuntu_22.04/amd64/

## Friday, September 30, 2022, 4:37:01PM EDT

* Start Kubernetes installation from scratch (`kubeadm`)
* Decided to disable swap even though 1.22 has "alpha" support for it
* Stole an Ansible playbook to disable swap (see [ansible](ansible))
* Discovered OPNsense will take an ssh command (ex: `ssh root@opnsense ls`)
* Decided *not* to do any Ansible management of OPNsense
* Created Ansible [inventory](ansible/inventory) file (in git) with groups by hardware
* Did `ansible -i inventory all -m ping` to see all respond
* Did `ansible -i inventory all -m setup | tee setup.out`
* Decided to use `-i inventory GROUP` by default
* Learned how to "dry run" an Ansible playbook
* Disabled swap from [swapoff.yaml](ansible/swapoff.yaml)
* Check mac address is unique with [script](ansible/check-uniq-mac)
* Check product_uuid is unique with [script](ansible/check-uniq-product_uuid)
* Discovered how to combine [bash script](ansible/ls-all) calling
  `ansible-playbook`
*  

```
ansible-playbook -i inventory -l all -K swapoff.yaml
ansible -i inventory all -m command -a 'free -h'
ansible -i inventory all -m command -a 'ip link'
```

Related:

* New in Kubernetes v1.22: alpha support for using swap memory \| Kubernetes  
  https://kubernetes.io/blog/2021/08/09/run-nodes-with-swap-alpha/
* Ansible: How to disable swap : linuxadmin  
  https://www.reddit.com/r/linuxadmin/comments/flzx5r/ansible_how_to_disable_swap/

## Friday, September 30, 2022, 1:01:29PM EDT

* Tested Dell Optiplex IOPS (3443)
* Retested Tridents IOPS (41)
* Retested Mac Minis IOPS (84)
* Retested HP Z640 IOPS (294)
* Decided to use /etc/hosts instead of DNS for now
* Discovered /etc/hosts allows ssh command tab completion
* Considering setting up NFS for /home on everything
* Considering setting up Samba USB drive for /home on everything

## Thursday, September 29, 2022, 11:26:19PM EDT

* Added Dell Optiplex control plane machines (arrived today)

## Wednesday, September 28, 2022, 3:27:50PM EDT

* Reconfigured network to use 192.168/16 (no IPv6)
* Using 192.168.1.1/16 for gateway
* Using 192.168.1/16 for DHCP (IPv4)
* Using 192.168.0/16 for static VMs
* Using 192.168.2/16 for Macs
* Using 192.168.3/16 for MSI Tridents
* Using 192.168.4/16 for HP (and future) VM host servers
* Using 192.168.42/16 for control plane nodes
* Using 8.8.8.8/8.8.4.4 for DNS until CoreDNS setup
* Upgraded OPNsense to 22.7.4 from 22.7 (known WAN IPv6 issues)
* Finished all cabling for rack 2
* Decided to buy HDMI keystone adapters for MSI Tridents
* Learned CIS security concern for IPv6 is dated and insignificant
* Created netplan.yaml template

## Tuesday, September 27, 2022, 3:27:40PM EDT

* Determined that initial `fio` output was completely wrong
* Got 100+ IOPS on Tridents
* Got 50+ regularly on Mac Minis
* Still keeping the control plane machines ordered with SSD
* Decided to use 172.16/12 (class B) instead of others
* Decided to soon drop IPv6 completely from LAN
* Decided to use example.com for domain since only relevant in docs
* Decided to use latest Kubernetes in personal cluster
* VMs on HP will be for testing different Kubernetes versions
* Current latest stable version of Kubernetes is 1.24 LTS
* Decided to use etcd version 3.5.5 (approved for LTS)
* Wrote `install-etcd` to get the latest version from GitHub
* Installed `jq` on target control planes
* Read about initial etcd setup
* Realized etcd (effectively) requires static IP addresses
* Abandoned notion of "just use DCHP registered names" for home lab

Related:

* https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.24.md
* https://github.com/kubernetes/kubernetes/blob/8cbe9e91c8b39de3dbb3b7e1eb76c586846721c5/cmd/kubeadm/app/constants/constants.go#L469-L483
* Clustering Guide \| etcd  
  https://etcd.io/docs/v3.4/op-guide/clustering/
* https://github.com/kelseyhightower/etcd-production-setup

## Tuesday, September 27, 2022, 1:14:36PM EDT

* Ordered faster IOPS Dell Optiplex machines for control plane
* Going to use NFS and OpenEBS StorageClass
* Learn `strace`, it's one of top 5 most important ops tools to learn

Encountered annoying AF_INET6 errors that prevent command-line searching
and research:

```
connect(3, {sa_family=AF_INET6, sin6_port=htons(443), sin6_flowinfo=htonl(0), inet_pton(AF_INET6, "2600:1f18:2489:8202:5162:2cb:b813:121f", &sin6_addr), sin6_scope_id=0}, 28) = -1 EALREADY (Operation already in progress)
pselect6(4, NULL, [3], NULL, {tv_sec=0, tv_nsec=100000000}, NULL) = 1 (out [3], left {tv_sec=0, tv_nsec=35265649})
connect(3, {sa_family=AF_INET6, sin6_port=htons(443), sin6_flowinfo=htonl(0), inet_pton(AF_INET6, "2600:1f18:2489:8202:5162:2cb:b813:121f", &sin6_addr), sin6_scope_id=0}, 28) = -1 ETIMEDOUT (Connection timed out)
close(3)                                = 0
socket(AF_INET, SOCK_STREAM, IPPROTO_TCP) = 3
ioctl(3, FIONBIO, [1])                  = 0
```

I need to disable handing out DHCP6 addresses to my home network to fix
other problems as well.

Turns out this specific problem is all from ipv6 not working with Ubuntu
Server and these applications and can be corrected by disabling IPV6 on
a specific machine (which is also turns out, is a part of standard CIS
hardening). This change is not persistent, however, unless you change it
in /etc/sysctl.conf which is probably not necessary.

```
sudo sysctl net.ipv6.conf.all.disable_ipv6=1
```

Or could just do this for everything.

```
sudo ua enable cis
```

Related

* https://media.geeksforgeeks.org/wp-content/uploads/20220330131350/StatediagramforserverandclientmodelofSocketdrawio2-448x660.png

## Friday, September 23, 2022, 9:57:09AM EDT

Began etcd installation but discovered Mac Minis have 26 IOPS and MSI
Tridents have 50. The recommended minimum is 50 from the etcd docs. This
video has a lot of frustration as I try to even figure out how to
determine a reliable IOPS value (ultimately with fio) but worth it.

Other etcd installation notes:

• Only “tier 1” supported OS is Linux on AMD64
• 2-4 cores is fine for less than “thousands of clients”
• 8 GiB RAM for less than “thousands of clients”
• “Disk speed is most critical factor”
• Hardware recommendations \| etcd https://etcd.io/docs/v3.5/op-guide/hardware/
• https://www.ibm.com/cloud/blog/using-fio-to-tell-whether-your-storage-is-fast-enough-for-etcd

First make a directory for `fio` to use.

```
mkdir test-data
```

Then start the test on a quiet system.

```
sudo fio --rw=write --ioengine=sync --fdatasync=1 --directory=test-data --size=22m --bs=2300 --name=mytest
```

## Thursday, September 22, 2022, 8:29:02PM EDT

* Dropping `fluff` because redundant to libvirt and Firecracker.

* Start to research Kubernetes installation starting with `etcd`.
  Decided on three external instances for HA simulation without too much
  overhead on these little Mac Minis.

## Thursday, September 22, 2022, 7:36:50PM EDT

* Spent a lot of time calculating energy budget and wattages.

* Bought a Protectli Vault after getting freaked out by the Netgear
  Nighthawk firmware update. Downloaded and installed pfSense. Then a
  friend talked me into trying OPNsense so installed and prefer.

* Experienced strange freezes with `cpu_loop` caused by VMware with my
  `anton` VM. Turned out to be unrelated to new network changes.
  Probably from the KVM dying.

* Startec KVM died. Not sure why. Going to try to get a new one on
  warranty. Added two keyboards on my desk for now (one for work, one
  for home lab streaming/gaming rig).

* Made mistake of buying 20A PDU with plugs that don't match sockets.

* Got everything cabled up. Turned up short an MSI power adapter, so
  waiting on that and my (lazy) purchase of 0.5' and 1' cables.

* Did the research in the apartment to find all the circuits and what
  they power. Bathroom receptacle are combined (2 of them) 20A. Turns
  out the master bedroom is on its own 15A circuit.

Related:

* Protectli Vault FW4B - https://a.co/d/6OU2p3M

## Tuesday, September 13, 2022, 4:53:48AM EDT

* Decided to use SSH as primary distributed configuration and RPC
  method. This means I have have entirely agent-less (ssh) configuration
  management dependency (using Ansible, etc.). This also means that
  eventually Bonzai agent, will have full ssh server support embedded
  into it as other prominent apps have done as well. SSH is the dominant
  networking protocol for such things. This also means will be
  abandoning plans to use GPG and/or mTLS/gRPC in Bonzai agent in favor
  of SSH.

* Install Ansible into workstation VM "control node" (anton, Ubuntu
  Server running on Windows 10 with VMware Workstation Pro).

* After hearing from community, decided to keep only inventory/hosts in
  dot files and put the rest of my custom playbook collection in a
  separate rwxrob/ansible repo.

* Create (or copy) an Ansible playbook to install CoreDNS on all three
  redundant servers that forwards to local router.

* Hate using Cloudflare (1.1.1.1/1.0.0.1) or Google (8.8.8.8/8.8.4.4)
  DNS (or any other centralized DNS) for anything, but gave in for
  Cloudflare since fastest. Such centralization is bad for the Internet,
  as we've seen when large sections of the Internet went dark (more than
  once) when CloudFlare when down.

* Consider alternative DNS providers (instead of ISP, which might be
  using Cloudflare or Google without us knowing).

* Determined that DNS over TLS (DoT) or HTTPS (DoH) isn't worth it for
  me (right now) but will experiment with it later.

* Run Ansible playbook to install CoreDNS on mini1, mini2, mini3.

* Decided against any adblocking/pie-hole stuff because of bad
  experience in the past with it being overly aggressive and just a PITA
  to keep configured properly.

* Decided on `lab.example.com` as fully qualified local zone domain
  (suggested by @j33pguy) so that examples and streaming and
  documentation all sync up from home. I won't be using a fully
  qualified domain at all for most things, but when I do, there's a good
  chance I'm going to want to document it for public consumption.

Related:

* Cloudflare Down Again  
  https://www.theverge.com/2022/6/21/23176519/cloudflare-outage-june-2022-discord-shopify-fitbit-peleton

* The Best Free and Public DNS Servers (September 2022)  
  https://www.lifewire.com/free-and-public-dns-servers-2626062

* Dropbear SSH  
  https://matt.ucc.asn.au/dropbear/dropbear.html

* What is DNS over TLS (DoT)? \| DDI (Secure DNS, DHCP, IPAM) \| Infoblox  
  https://www.infoblox.com/glossary/dns-over-tls-dot/

* What is DNS over HTTPS? Definition, Implementation, Benefits, and More  
  https://heimdalsecurity.com/blog/dns-over-https-doh/

* nielux: I learned Ansible from Jef Geerling, 10/10 would recommend his
  youtube series/book about it

* Roles --- Ansible Documentation  
  https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html

* https://www.oreilly.com/library/view/learning-coredns/9781492047957/ch04.html

## Monday, September 12, 2022, 5:54:43PM EDT

* Reviewed and update inventory of all available hardware.

* Discussed future power consumption needs and UPS possibilities.

* Determined to install CoreDNS before `etcd` so that nothing in the
  entire architecture does *not* have a DNS name. Thanks to @ryanwilcox
  for the reminder that doing so would prevent decision making now about
  the subnet and VLAN architecture. CoreDNS, which is written in Go,
  works very nicely since there is a Kubernetes plugin to use it
  externally (normally Kubernetes uses it internally, by default). Being
  in Go means that I can actually read and understand the source of the
  most important DNS server in the entire cloud native landscape. Also,
  it doesn't suck like Bind.

* CoreDNS: DNS and Service Discovery  
  <https://coredns.io/>

* Decided to use the `systemd` deployment since it has to be reliably up
  and managed as a service even though others may run it from a
  container. <https://github.com/coredns/deployment> `man sysusers.d`

#### FIXME

## Get server hardware

## Decide network architecture and system names

## Build rack and install hardware

## Install operating systems on servers

## Configure secure shell on all servers

Yes it's annoying to have to type the password every time, but that can
also be skipped if you prefer.

```
for i in `z yq '.hwnodes[].name'; do 'copy-ssh-id "$i"'; done
```

As soon as you have ssh keys on everything the rest is easier (or use
`sshpass` if you really want).

Once you have keys on everything then disable ssh passwords completely.

```
PasswordAuthentication no
KbdInteractiveAuthentication no
```

Note that the latter one used to be `ChallengeResponseAuthentication`
but that name has been deprecated.

## Setup NFS on a server

Choose a server with reasonable amount of storage space (`trident1`)

Install the NFS dependencies.

```
sudo apt-get update
sudo apt install nfs-kernel-server
```

## Setup main Kubernetes cluster

