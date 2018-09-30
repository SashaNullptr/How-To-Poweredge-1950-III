# How To: Dell Poweredge 1950 Gen. III

## What is the Project?

Suppose, in a strictly hypothetical scenario, you purchased a Generation III Dell Poweredge
from your local PC recycler ( who may have given you a 40% discount ). Now you have
a 1U rack server, made in 2009, and you're wondering what to do with it.

This is a totally opinionated guide to getting the absolute most of a Gen. III Poweredge.

## But Why?

Since hardware of this vintage has long since been phased out of data-centers,
parts are *cheap*. While it will never compete with modern hardware
on a watts / dollar basis, when fully spec'd out these systems are roughly equivalent to
an `c5d.4xlarge` AWS EC2 instance, which would run about \$550 per month. If you add
a decent GPU into the mix you can end up with something closer to a `p3.2xlarge`
instance, which would set you back \$2200 / month. If we assume power is about
$0.15 per kilowatt hour, we are looking at a monthly power bill in the \$30-50 dollar range.
This means if you need a big compute / build node Poweredge 1950's actually make
a decent option.

## The Draw Backs

For reasons we will discuss in the [hardware section](hardware/index.md) these machines
are LOUD. As in, non-trivial risk of hearing damage loud. If you live in a small
space there is no way to adequately mask the sound, and if you live in a large
space you need to make peace with a room that will be borderline inhospitable.
However, if you plan on colo'ing your system this will not make much of a difference.

These machines are also *not* particularly flexible when it comes to hardware. They
were never designed for high power PCIe devices, and those will only accept graphics
cards under duress. Modern servers that include auxillary 12 volt plugs and have
2U sized PCIe risers take to graphics cards and unusual PCIe hardware much, much
more easily.
