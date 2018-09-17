# Hosting a Hypervisor

## Motivation

So you have a big, fairly high-tech ( for 2009 ) 1U rack server set-up -- now what?
You could run one gigantic bare-metal single OS, but that would almost certainly be
inflexible and insecure. A more flexible approach, that is still fairly close to metal,
is to run a hypervisor, like [Xen](https://www.xenproject.org/) or [KVM](https://www.linux-kvm.org/page/Main_Page).

## Installing a Base Image

We will be using Debian Linux as our base image, for entirely
preferential reasons. The rest of the guide will only apply to Debian systems
but should provide a good template for other systems as well.

## Setting up KVM

## Install KVM and Supporting Utilities

```shell
sudo apt-get install qemu-kvm libvirt-clients libvirt-daemon-system bridge-utils
```

## Install Virt-Mananger

While not strictly necessary `virt-manager` provides a GUI for provisioning and
monitoring VMs.

```shell
sudo apt-get install virt-manager
```

## Set up Network Bridge

``` shell
# /etc/network/interfaces
# interfaces(5) file used by ifup(8) and ifdown(8)
auto lo br0
iface lo inet loopback

iface eth0 inet manual

iface br0 inet dhcp
    bridge_ports    eth0
    bridge_stp      off
    bridge_maxwait  0
    bridge_fd       0
```

``` shell
# /etc/network/interfaces
# interfaces(5) file used by ifup(8) and ifdown(8)
auto lo br0
iface lo inet loopback

iface eth0 inet manual

iface br0 inet dhcp
   bridge_ports eth0

```

## Set up PCIe Pass-through
