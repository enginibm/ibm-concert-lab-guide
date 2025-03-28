# Lab setup

Techzone
VM
Watsonx.ai (TZ ou celui d'IBM Cloud)

## Provision VM from techzone

VM - 16 vCPUs/32GB RAM/512GB Disk

## Prepare VM disk

logon in VM

```bash
ssh itzuser@169.44.147.111 -p 30288
sudo -i
mkfs.ext4 -m 0 -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/vdc
blkid | grep /dev/vdc

mkdir -p /mnt/concert
chmod 777 /mnt/concert

cp /etc/fstab /etc/fstab.orig
vi /etc/fstab
```

insert: UUID=6b6320a6-f7cb-45fa-9fc1-6aaedeeb8e18 /mnt/concert ext4 discard,defaults,nofail 0 0

```bash
mount -a
systemctl daemon-reload
lsblk
```

## install podman

```bash
sudo dnf install podman
sudo sysctl user.max_user_namespaces=15000
sudo usermod --add-subuids 200000-201000 --add-subgids 200000-201000 itzuser
```
