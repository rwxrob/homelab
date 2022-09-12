# Log of Work to Setup Homelab

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

