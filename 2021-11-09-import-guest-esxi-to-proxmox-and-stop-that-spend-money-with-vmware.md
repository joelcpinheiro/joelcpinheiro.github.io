---
title: Import guest ESXi to Proxmox and stop that spend money with VMware
slug: import-guest-esxi-to-proxmox-and-stop-that-spend-money-with-vmware
date_published: 2021-11-09T17:58:28.000Z
date_updated: 2021-11-09T18:00:47.000Z
tags: #Import 2023-04-07 15:04
---

I suppose that you know that need shutdown the guest and export to OVF, is it okay?

Copy the OVF files to a node from Proxmox and...

Now you will convert the disk on VMDK to QCOW2 format, executing the command below:

    qemu-img convert -f vmdk VIRTMACHINE-disk1.vmdk -O qcow2 VIRTMACHINE.qcow2

Login on Proxmox and CREATE the guest of custom that you want.

Now, remove the disk default to import the disk with the command below:

    qm importdisk IDVM VIRTMACHINE.qcow2 STORAGESHARED

Add the disk on the hardware configurations of guest like IDE device disk and update Boot Order on disk to IDE.

Now, you could power on the virtual machine with success and check if is very well =D

An observation, do you have to adjust the interface name on the guest, for example, interface ens192 was recreated to eth0.
