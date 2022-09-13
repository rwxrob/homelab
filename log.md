# Log of Work to Setup Homelab

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

* Never using Cloudflare (1.1.1.1/1.0.0.1) or Google (8.8.8.8/8.8.4.4)
  DNS (or any other centralized DNS) for anything. Such centralization
  is bad for the Internet, as we've seen when large sections of the
  Internet went dark (more than once) when CloudFlare when down.

* Consider alternative DNS providers (instead of ISP, which might be
  using Cloudflare or Google without us knowing).

* Determine if DNS over TLS (DoT) or HTTPS (DoH) is worth it?

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

