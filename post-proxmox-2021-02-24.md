---
layout: page
permalink: /post-proxmox-2021-02-24
title: Proxmox tips
---


# Import guest ESXi to Proxmox and stop that spend money with VMware

I supose that you know that need shutdown the guest and export to ovf, it's okay?

Copy the OVF files to a node from Proxmox and...

Now you will convert the disk on VMDK to QCOW2 format, executing the command below:

```sh
qemu-img convert -f vmdk VIRTMACHINE-disk1.vmdk -O qcow2 VIRTMACHINE.qcow2
```

Login on Proxmox and CREATE the guest of custom that you want.

Now, remove the disk default to import the disk with the command below:

```sh
qm importdisk IDVM VIRTMACHINE.qcow2 STORAGESHARED
```

Add the disk on the hardware configurations of guest like IDE device disk and update Boot Order on disk to IDE

Now, you could power on the virtual machine with success and check if is very well =D

An observation, do you have adjust the interface name on the guest, for example, interface ens192 was recreated to eth0.

