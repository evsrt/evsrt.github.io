---
layout: default
title: proxmox
parent: KB
---
# Import OVA as Proxmox VM
Import OVA as Proxmox VM <https://www.itsfullofstars.de/2019/07/import-ova-as-proxmox-vm/>
# qm importdisk
```bash
# vdi
qm importdisk 109 /root/testing.vdi local-lvm
# ova
qm importdisk 101 disk1.vmdk local-lvm -format qcow2
# proxmox 8.x
qm disk import vmid windows.vdi storage
```
