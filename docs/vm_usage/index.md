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

## Set up a Network Bridge

In order for any of our VMs to have network access we need to set up a network bridge
to our host system. There are quite a few ways to do this, but one of the most
straight-forward ways is to edit `/etc/network/interfaces` and set up a network bridge.
Note that `eth0` in the example files is just the name of the Ethernet
adapter. The Poweredge 1950 has *two* NICs built-in which are labeled `eth0` and
`eth1`so we could have just as easily used `eth1`.

Further note that Ethernet adapter names are *not* guaranteed to be consistent across OSes and hardware. If this guide is being adapted to other hardware of systems the name will
need to be resolved and `eth0` will need to be replaced in the interface file.
To list all network interface devices, simply use `ls /sys/class/net`.

### Public Virtual Bridge

In general not only want our VMs to be able to access network resources, but we
also want to be able to access them without needing to go through the host machine.
In order to do this we need a public bridge, which will expose any guest VMs on
as machines on the same network as the host.

Below is an example interfaces that will set-up a public bridge. Whenever we
modify the `/etc/network/interfaces` file we will need to reboot the host machine
or run `/etc/init.d/networking restart`.

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

## Set up PCIe Pass-through
