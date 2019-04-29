.. role:: raw-html-m2r(raw)
   :format: html


`Documentation main page <https://frinxio.github.io/Frinx-docs/>`_
`VPP Operations Manual main page <https://frinxio.github.io/Frinx-docs/FRINX_VPP_Distribution/operations_manual.html>`_

Qemu + VPP + Vhost user
=======================

This article shows you how to set up a VM or a bare metal machine with qemu and KVM and VPP handling L2 networking between VMs.

*  `Resources <#resources>`__
*  `Install Qemu/KVM <#install-qemu-kvm>`__
*  `Inside PROXMOX <#inside-proxmox>`__
*  `Install VPP <#install-vpp>`__
*  `Configure VPP <#configure-vpp>`__
*  `Start VMs <#start-vms>`__
*  `Network config in VMs <#network-config-in-vms>`__
*  `Ifconfig <#ifconfig>`__
*  `Troubleshooting <#troubleshooting>`__

Resources
---------

The main resource for the setup was: https://gist.github.com/egernst/5982ae6f0590cd83330faafacc3fd545

Install Qemu/KVM
----------------

The installation is straightforward when following this guide *(Make sure KVM is available)* : https://help.ubuntu.com/community/KVM/Installation

Inside PROXMOX
--------------

It is also possible to do all this within a VM utilizing nested virtualisation. With e.g. PROXMOX, it's just a matter of turning on nested virtualisation according to: https://pve.proxmox.com/wiki/Nested_Virtualization 
After that, a VM can be installed with Qemu and KVM as if it were a bare metal machine.

Install VPP
-----------

To install VPP, see this guide: https://wiki.fd.io/view/VPP/Installing_VPP_binaries_from_packages
and start it up...

Configure VPP
-------------

The following configuration can be used to connect two VMs using VPP as the vhost interface server side:

.. code-block:: guess

   sudo vppctl create vhost socket /tmp/sock0.sock server
   sudo vppctl create vhost socket /tmp/sock1.sock server

   sudo vppctl set interface state VirtualEthernet0/0/0 up
   sudo vppctl set interface state VirtualEthernet0/0/1 up

   sudo vppctl set interface l2 bridge VirtualEthernet0/0/0 1
   sudo vppctl set interface l2 bridge VirtualEthernet0/0/1 1



This configuration is SERVER for VPP and CLIENT for VMs and there is a bridge domain between interfaces in VPP

Start VMs
---------

**FreeBSD**\ :raw-html-m2r:`<br>`
The following command starts FreeBSD VM and attaches it to the opened socket by VPP:

.. code-block:: guess

   sudo qemu-system-x86_64 \
       -enable-kvm -m 1024 \
       -smp 4 -cpu host \
       -drive file="/home/test/FreeBSD-10.3-RELEASE-amd64.qcow2",if=virtio,aio=threads \
       -chardev socket,id=char1,path=/tmp/sock0.sock \
       -netdev type=vhost-user,id=mynet1,chardev=char1,vhostforce \
       -device virtio-net-pci,mac=00:00:00:00:00:a2,netdev=mynet1 \
       -object memory-backend-file,id=mem,size=1024M,mem-path=/dev/hugepages,share=on \
       -numa node,memdev=mem -mem-prealloc \
       -debugcon file:debug.log -global isa-debugcon.iobase=0x402 \
       -vnc :1



The image was taken from: `http://ftp.freebsd.org/pub/FreeBSD/releases/VM-IMAGES/10.3-RELEASE/amd64/Latest/ <https://pve.proxmox.com/wiki/Nested_Virtualization>`_

**Ubuntu 14** :raw-html-m2r:`<br>`
The following command starts Ubuntu14 VM and attaches it to the opened socket by VPP:

.. code-block:: guess

   sudo qemu-system-x86_64 \
       -enable-kvm -m 2048 \
       -smp 4 -cpu host \
       -drive file="/home/test/disk.img",if=virtio,aio=threads \
       -drive file="/home/test/my-seed.img",if=virtio,aio=threads \
       -chardev socket,id=char2,path=/tmp/sock1.sock \
       -netdev type=vhost-user,id=mynet2,chardev=char2,vhostforce \
       -device virtio-net-pci,mac=00:00:00:00:00:a3,netdev=mynet2 \
       -object memory-backend-file,id=mem,size=2048M,mem-path=/dev/hugepages,share=on \
       -numa node,memdev=mem -mem-prealloc \
       -debugcon file:debug2.log -global isa-debugcon.iobase=0x402 \
       -vnc :2



The image was taken from: https://uec-images.ubuntu.com/releases/14.04/release/ The command does not use the qcow image directly. Instead it uses the following guide to configure password login to the VM according to this guide: https://help.ubuntu.com/community/UEC/Images (Section: Ubuntu Cloud Guest images on 12.04 LTS (Precise) and beyond using NoCloud)

... Ubuntu cloud images do not allow password login via e.g. VNC


Network config in VMs
---------------------

**Accessing VMs** :raw-html-m2r:`<br>`
Each VM can be accessed using VNC at:

.. code-block:: guess

   <IP>:590<VNC_PORT>



so in the case of the earlier FreeBSD example and IP being e.g. 10.10.199.72 it would be:

.. code-block:: guess

   10.10.199.72:5901



Credentials:

..

   ..

      The default credentials for VMs are:


   **FreeBSD** : root, no password\ :raw-html-m2r:`<br>`
   **Ubuntu** : ubuntu, passw0rd


Ifconfig
--------

The configuration for the VMs could look like:  

FreeBSD:
~~~~~~~~

.. code-block:: guess

   ifconfig vtnet0 10.10.55.1
   ifconfig vtnet0 inet6 add 2001:0db8:0:f101::1/64



Ubuntu:
~~~~~~~

.. code-block:: guess

   ifconfig eth0 10.10.55.2
   ifconfig eth0 inet6 add 2001:0db8:0:f101::2/64



Now, ping and any other traffic should be working fine between VMs.

Troubleshooting
---------------

VPP vhost user bug: Fix: https://gerrit.fd.io/r/#/c/6735
