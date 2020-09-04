# High Level Overview

Before starting a project like this, you should have an idea of what we're trying to build.

![server](../images/q25b.jpg)

We're building a media server. By my defininition this means it will be running 24/7, need lots of hard drives, to organically grow with us over time as our media collections do and to earn its keep by running applications reducing our dependence on hosted services. Self-host all the things!

## Requirements



By defining some criteria, we can objectively select software to:

* act as a NAS type device serving files across the network
* support the primary use-case of "write once - read many" for media files
* support hard drives of differing / mismatched sizes
* support incremental upgrading of hard drives in batches as small as one
* each drive should have a separately readable filesystem
* group multiple hard drives under a single mount point for reads and writes
* spin up only the drive(s) in use
* provide fault tolerance to protect against (inevitable) hard drive failure
* checksum files to guard against bitrot
* saturate a gigabit LAN connection (be performant)
* run multiple applications at once sandboxed

## Software overview