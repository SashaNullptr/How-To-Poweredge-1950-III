# Provisioning

## Initial Set-Up

For the duration of this guide we will be referencing a Debian 9.5 base system.
The names of packages and specific installation steps will almost certainly differ
if a different distro or Debian version is used.

## Disk Partitioning Scheme

The Poweredge 1950 in question is equipped with 4x 2.5" HDD bays that connect to the
back-plane and PERC 6/i RAID controller. In addition the optical drive bay was removed
and replaced with a housing for an additional 2.5" HDD which is attached to the internal
SATA 1.0 port.

These drive bays are populated with the following hardware:

| Back-plane | Independent SATA 1.0 |
| - | - |
| 4 x Seagate Constellation.2 1 TB, 7200 RPM | 1 x Seagate Spinpoint M9T 2TB, 5400 RPM |

These resources will be used in the following way:

| Base OS | General Storage | Low Speed NAS |
| - | - | - |
| 120 GB RAID 0 | ~2.67 GB RAID 5 | ~ 2 TB Independent |

In practice a RAID 5 array with the hardware listed above yields read/write speeds of
\~ 300 MB/s and the independent 2 TB drive yields \~ 100 MB/s. We want to reserve the
(relatively) high speed volumes for use by our base OS and for applications where
I/O performance might be a consideration. The SATA 1.0 port on the main
board is not managed by the RAID controller and any attached HDD has to be used as a totally independent
volume. This means that we will have access to almost the full storage available on
the disk, but lower I/O speeds. Because of this we reserve the 2 TB volume for
long-term storage or for applications where I/O performance is not critical.

### Setting up RAID Arrays


### Setting up Extra SATA HDD

### Firmware

The Ethernet adapters used by the Poweredge 1950 require firmware from the
`firmware-bnx2_0.43` package in order to operate. This firmware can be installed during initial
set-up or afterwards. For obvious reasons it's easier to install this package
_during_ initial installation, because once the system is set-up without it we
won't have access to the internet and will need to install the package manually
using removable media of some sort.

#### Base + Non-Free LiveCD

There is [a special LiveCD](http://cdimage.debian.org/cdimage/unofficial/non-free/cd-including-firmware/9.5.0+nonfree/amd64/iso-cd/) image that includes the base Debian 9.5
system and all of the "non-free" firmware, of which the `bnx2` firmware is a subset.
Using this image should mean the Ethernet adapters function normally out-of-the-box.

#### Manual Installation During Install

Alternatively a base Debian image can be used and the firmware can be loaded
during the LiveCD installation. The [compressed firmware file package](http://cdimage.debian.org/cdimage/unofficial/non-free/firmware/stretch/current/)
needs to be downloaded, uncompressed, and placed on a removal media device.
During installation the missing `firmware-bnx2_0.43` will be identified. By selecting `firmware-bnx2_0.43.all.deb` file on the previously prepared removal media device,
the missing firmware will be installed and the Ethernet adapters should function normally.

#### Manual Installation After Install

We can also install our missing firmware _after_ the base OS is set-up. Once again
we need to grab the [compressed firmware file package](http://cdimage.debian.org/cdimage/unofficial/non-free/firmware/stretch/current/)
 uncompress it, and place the uncompressed files on a removal media device.

We can then copy the `firmware-bnx2_0.43.all.deb` file onto the host system and
install it with `dpkg`.

```shell
sudo dpkg -i firmware-bnx2_0.43.all.deb
```
Since we didn't have a network connection accessible during the LiveCD installation
our `apt` sources will be set to local LiveCD versions. Now that we have the proper
firmware installed to use our Ethernet adapter we need to change out apt sources
to reference the main Debian repos instead of these local versions.

We need to open up `/etc/apt/sources.list` using whatever text editor we choose...

```shell
sudo nano /etc/apt/sources.list
```

... and replace the entire file with the following.

``` apacheconf
deb http://deb.debian.org/debian stretch main
deb-src http://deb.debian.org/debian stretch main

deb http://deb.debian.org/debian-security/ stretch/updates main
deb-src http://deb.debian.org/debian-security/ stretch/updates main

deb http://deb.debian.org/debian stretch-updates main
deb-src http://deb.debian.org/debian stretch-updates main
```

Unfortunately these changes will result in target conflicts.. In order to resolve these conflicts we simply need to remove the file
`base.list` under `/etc/apt/source.list.d`.

```shell
sudo rm /etc/apt/source.list.d/base.list
```

With these changes in place we can update our `apt` sources to reference the
standard Debian repos instead of the LiveCD local ones.

```shell
sudo apt-get update
```

### Installation Details

We will be installing the OS on the small ~120 GB RAID 0 partition we made earlier.

If we used the "guided partition" option offered by the Debian installer it will
automatically allocated ~64 GB of space to `swap`. Since our system has 64 GB of RAM
this is a truly excessive amount of `swap` space. We can either partition our system
manually during installation, or after installation using `gparted` or similar.

## Mounting HDDs

By default only the small 120 GB partition that holds the OS will be mounted on
boot. We need to set up mount points for our 2.6 TB RAID 5 array and the
independent 2 TB HDD.
