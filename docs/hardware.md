# Hardware

## Processor

The Poweredge 1950 comes equipped with 2 LGA771 processor slots.

The best processor for this socket, based on PassMark score, seems to be the Intel Xeon X5450.
This is a quad-core, none hyperthreaded processor that runs at 3.00 GHz.

### Caveats

X5450's use **a lot** of power, with a TDP of 120 Watts each! A machine
equipped with two X5450's, 64 GB of RAM and 4 2.5" HDDs will draw over 300 Watts
**at idle**. This can be a particular concern if you plan on colo'ing a unit
and pay by the amp-hour, or have an amperage limit.

## RAM

Up to 64 GB of DDR2 FB-DIMM ( Fully Buffered ) Ram, as 8 x 8 GB sticks, is supported. Any brand of memory
should suffice, but [A-Tech](https://www.amazon.com/gp/product/B00J8QK43A/)
produces some of the cheapest 64 GB RAM bundles.

### Caveats
Almost *any* after market memory suffers from a fairly serious issue. The
Base Board Management Controller ( BMC ), the system that controls low-level
devices such as the power supplies, fans and so forth, will not be able to read
the temperature sensor integrated into each RAM stick. This will result in the
cooling fans running *much* faster than usual under normal conditions
-- for example idle conditions will produce speeds around 9-10K RPM instead of about
3-4.5K.
As a result the server will be very, very loud.

While there are ways to somehwhat control the BMC via Dell provided tools and
custom BMC image flashing, none of these methods allow the fan speed
threshold to be controlled.

## Storage

### Stock Hard Drive Bays

The Poweredge 1950 could either be optioned with two 3.5" quick-swap HDD bays
or 4 2.5" bays. These drives are connected to the back-place and the integrated
RAID controller

### An extra SATA port

Generation I and II versions of the Poweredge 1950 used an IDE bus to control the
integrated optical drive. Generation III models added a lone SATA I bus to the
main board, which was designed to replace the only IDE bus and control a SATA
optical drive. Gen. III main power lead also had a small break-out cable to supply
the 5 volts required by the optical drive.

As fate would have it, the SATA bus
and 5 volt power are exactly what is required to run a 2.5" HDD. By using an
"Optical drive to HDD adapter", such as [this one](https://www.amazon.com/gp/product/B004XIU4T2/)
the optical drive can be swapped out for an additional 2.5" HDD. However this drive
will *not* be accessible by the RAID controller and cannot be assigned to a RAID
array with the main drives that connect to the back-plane.

### An extra USB port

Generation III models of the Poweredge 1950 included a very curiously positioned
USB port in the middle of the main board. Apparently this was supposed to be used
for cryptography hardware keys -- fortunately it is just a normal USB 2.0 port.
This allows for some fairly inserting possibilities. For example, with some clever
cable routing, a [USB-to-SATA converter](https://www.amazon.com/gp/product/B00HJZJI84/)
and a [2.5" HDD PCIe slot adapter](https://www.amazon.com/gp/product/B0080V73RE/) an
extra HDD can be added to the system, assuming there are empty PCIe slots available.
There is also *just* enough space for a small USB thumb drive,  

### An extra SAS port

The integrated RAID controller comes equipped with *two* SAS ports, only one of
which is used by the back-plane. This is probably due to the RAID controller being
shared between the Poweredge 1950 and 2950, a unit that could support many more
HDDs. By using a SAS-to-SATA break-out cable, [like this one](https://www.amazon.com/gp/product/B00S7KTUXS/) up to 4 additional HDDs can be
attached to RAID controller. Of course there is
no additional power or space available inside the server case, but if an external
drive enclosure and power supply is used 4 HDDs can be provisioned by the RAID controller.

## Using Graphics Cards

The Poweredge 1950 was produced in an era before GPGPU computing was in vogue
and was not built with high-power use PCIe devices in mind. However, with enough
work GPUs *can* be used

### PCIe Slots Available

The Poweredge 1950 can be equipped with either two PCI-X risers, or two PCIe 2.0
8x risers. Note that these *cannot* be mixed and matched, it must be either two
PCI-X risers, or two PCIe risers.

Since there are a very limited number of PCI-X graphics cards available, none of
which are any good, for GPU use the two PCIe risers will are almost always used.

### Caveats

The PCIe 2.0 standard dictates that PCIe slots should provide 25 watts of power to
standard devices and up to 75 watts to devices identified as "high power" such as
graphics cards. However, it seems that the 8x PCIe slots do *not* comply with
this part of the standard and will only supply up to 25 watts to any device.

### Using a Graphics Card as a VGA Device

In order to use graphics cards as VGA devices, which
is required for graphics output and OpenCL/CUDA device use, the iDRAC
remote management card will need to be removed. In general the system will boot
just fine with the iDRAC in place, but any attached graphics cards will not show
up as valid VGA devices on boot. By removing the iDRAC and resetting the BIOS
graphics cards can be used as VGA devices, as per usual.

### Using Graphics Cards that Require <= 25 Watts

Graphics cards that draw less than 25 watts will, generally not run afoul
of power draw issues. However not every card will be recognized by the BIOS, and
there doesn't appear to be any way to tell *a prior* which cards will work.

### Using Graphics Cards that Require > 25 Watts

Any card that is rated at > 75 watt power usage will generally cause a POST failure
with an PCIe riser error, assuming only power from the PCIe slot is used. However,
if 12 volt power is supplied from an external source the card will usually work
as intended. As a word of caution a power supplied to the card should always use
the same ground reference. That is, using the 12 volt power from the PCIe rail
and auxiliary power from an external power supply is *not recommended*.
Power should either be supplied from the 12 volt sources available on the main board
( or the two power supplies ) or entirely from an external power supplies, as is the
case when using a PCIe extender and external power supply.

### Resetting the BIOS

Any time the iDRAC card is removed from or added to the system the BIOS will need
to be reset or the system will hang on POST. The order to reset the BIOS the
`NVRAM_CLR` jumper will need to be set to the "clear" position and the system will
need to be boot cycled.

A page from the maintenance manual for setting the `NVRAM_CLR` jumper is shown below.

![this page](static_assets/dell_poweredge_1950_bios_reset_pg.png)

### List of Graphics Cards Known to Work

* PNY GeForce GT 720 1GB DDR3
* Sapphire Nitro Radeon R7 360

### List of Graphics Cards Known NOT to Work

* Nvidia GT 1050(?)