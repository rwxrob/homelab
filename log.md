# Log of Work to Setup Homelab

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

