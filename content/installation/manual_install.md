# Manual Install

This section covers manual installation of the key components required to run a Perfect Media Server. 

# Ubuntu 20.04 Installation

## User creation

+ GID and UID stuff

# docker installation

For our container runtime, we'll be using docker. There are other options in this space but for now, docker is the most mature - \[for more info see **[containers](../concepts/containers.md)]\]**.

We are using Ubuntu Linux which means docker installation is easy and well supported. See the [docker documentation](https://docs.docker.com/engine/install/ubuntu/) for full details.

## docker-compose installation

`docker-compose` is a tool for defining and running multiple containers at once using docker. Installing compose is optional but highly recommended as it drastically simplifies container lifecycle management - \[for more info see **[containers](../concepts/containers.md)]\]**.

Installation instructions for Linux can be found [here](https://docs.docker.com/compose/install/#install-compose-on-linux-systems).

# MergerFS installation

[MergerFS](https://github.com/trapexit/mergerfs) is the (not so) secret sauce that makes Perfect Media Server possible.

Installation in Ubuntu can be performed using `apt` but the version in the Ubuntu repository is usually a little behind upstream. For example, at the time of writing Ubuntu provides `2.28.1` which was released in June 2019. The latest upstream release was August 2020 with version `2.30.0`. Therefore, it is **not** recommended to install mergerfs from the Ubuntu repos.

Instead, navigate to the mergerfs github [releases](https://github.com/trapexit/mergerfs/releases) page and find the correct `.deb` file for Ubuntu 20.04. For example:

```
## Download and install - ensure to update version number!
wget https://github.com/trapexit/mergerfs/releases/download/2.30.0/mergerfs_2.30.0.ubuntu-focal_amd64.deb
sudo dpkg -i mergerfs_2.30.0.ubuntu-focal_amd64.deb

## Verify installation
alex@cartman:~$ apt list mergerfs
Listing... Done
mergerfs/now 2.30.0~ubuntu-focal amd64 [installed,local]
```

# Mounting drives manually

> Due to some differences in the way in which Ubuntu server and desktop present drives to us under `/dev/disk/by-id` we recommend using the Ubuntu desktop variant.

TODO: Info
You may now connect your data disks. 

In order to use these disks our OS needs to mount them. *Mounting* means that we are providing the OS with instructions on how to read or write data to a specific drive. The most common way of configuring drives for use with PMS is to create one large partition which spans the entire drive, format it to `ext4` or `xfs` and then mount it.

The filesystem wars have raged for decades and there is no right or wrong one to pick. However, we recommended either `ext4` or `xfs` to keep things simple. XFS allegedly works slightly better with large files (like media files) but there is not much in it. Red Hat have a great article on choosing your filesystem [here](https://access.redhat.com/articles/3129891).

Remember with MergerFS you are able to safely mix and match filesystems and drive sizes.

## Identifying drives

List all drives in a system with:

    ls /dev/disk/by-id

The output will look something like this:

```
root@cartman:~# ls /dev/disk/by-id
ata-HGST_HDN728080ALE604_R6GPPDTY                     ata-WDC_WD100EMAZ-00WJTA0_2YJ373DD
ata-SPCC_Solid_State_Disk_BA1B0788165300033582        ata-WDC_WD100EMAZ-00WJTA0_2YJ373DD-part1
ata-SPCC_Solid_State_Disk_BA1B0788165300033582-part1  ata-WDC_WD100EMAZ-00WJTA0_2YJ373DD-part9
ata-WDC_WD100EMAZ-00WJTA0_2YJ2S3AD                    ata-WDC_WD100EMAZ-00WJTA0_2YJ7E2VD
ata-WDC_WD100EMAZ-00WJTA0_2YJ2S3AD-part1              wwn-0x5000cca263c9dc2c
```

We now need to create a map between ephemeral drive mappings such as `/dev/sdc` and `ata-HGST_HDN728080ALE604_R6GPPDTY`. We can do this using `ls -la /dev/disk/by-id/ata-HGST_HDN728080ALE604_R6GPPDTY`. The following output is generated:

```
root@cartman:~# ls -la /dev/disk/by-id/ata-HGST_HDN728080ALE604_R6GPPDTY
lrwxrwxrwx 1 root root 9 Sep  9 23:08 /dev/disk/by-id/ata-HGST_HDN728080ALE604_R6GPPDTY -> ../../sdc
```

Therefore, we can ascertain that `/dev/sdc` is mapped to this physical drive. Never use `/dev/sdX` as a long term solution for drive identification as these identifiers can and do change without warning due to other hardware changes, kernel upgrades, etc. The `/dev/disk/by-id` identifier is tied to that specific piece of hardware by drive model and serial number and will therefore never change.

## Brand new drive

Before we create a partition on a brand new disk, ensure you have 'burned it in' as we cover under *Hardware Considerations* -> (New Drive Burn-In[../hardware/new_drive_burnin.md].

> **BE CAREFUL HERE** - We are about to perform destructive steps to the partition table of the drive. If there is *any* existing data on this drive - **IT WILL BE WIPED**. Make sure you proceed with caution! You have been warned!

The following steps will require root access, become the root user by typing `sudo su`. Using our example drive from the prior section we will use `gdisk` to create a new partition and filesystem. Run `gdisk /dev/sdX` (replacing `sdX` with your drive), for example:

    root@cartman:~# gdisk /dev/sdc
    GPT fdisk (gdisk) version 1.0.5

    Partition table scan:
        MBR: protective
        BSD: not present
        APM: not present
        GPT: not present

Once `gdisk` is loaded we are presented with an interactive prompt `Command (? for help):`. To see all options simply type `?`. In the initial output from gdisk we can see there is no partition table present on this drive - it's a good sanity check you have the right drive before erasing the partition and file allocation tables.

Use the following sequence to create one large partition spanning the entire drive - **note this is destructive**.

* `o` - creates a new **EMPTY** GPT partition table (GPT is good for large drives over 3TB)
    * `Proceed? (Y/N) - Y`
* `n` - creates a new partition
    * Partition number (1-128, default 1): `1`
    * First sector (34-15628053134, default = 2048) or {+-}size{KMGTP}: `leave blank`
    * Last sector (2048-15628053134, default = 15628053134) or {+-}size{KMGTP}: `leave blank`
    * Hex code or GUID (L to show codes, Enter = 8300): `8300`
* `p` - (optional) validate 1 large partition to be created
    * `Model: HGST HDN728080AL`
    * `Number  Start (sector)    End (sector)  Size       Code  Name`
    * `1       2048              15628053134   7.3 TiB    8300  Linux filesystem`
* `w` - writes the changes made thus far
    * Until this point, gdisk has been non-destructive  

### Create a filesystem

Create an `ext4` filesystem thus:

    mkfs.ext4 /dev/sdX1

Congratulations! Your new drive is now formatted and ready to store data. Move onto the next section 'Existing drive' to learn how to mount it (make it available to the OS for use).

## Existing drive

[Identify](#identifying-drives) the existing drive and take note of the partition you wish to mount. This is usually displayed as `-part1` using `/dev/disk/by-id`. Ensure you have the correct supporting libraries for your filesystem installed such as `xfsprogs` for XFS and then mount the drive manually like so:

    mkdir /mnt/manualdiskmounttest
    mount /dev/disk/by-id/ata-HGST_HDN728080ALE604_R6GPPDTY-part

Verify that the drive mounted and displays the correct size as expected:

    root@cartman:~# df -h
    Filesystem                        Size  Used Avail Use% Mounted on
    /dev/sdc1                         7.3T  2.8T  4.6T  38% /mnt/manualdiskmounttest

## Create mountpoints

Assuming the previous test went well, it's time to come up with a mountpoint naming scheme. We recommended `/mnt/diskN` because it makes the `fstab` entry for MergerFS simpler thanks to wildcard support (more on this shortly). For example:

    mkdir /mnt/disk{1,2,3,4}
    mkdir /mnt/parity1 # adjust this command based on your parity setup
    mkdir /mnt/storage # this will be the main mergerfs mountpoint

We also just created `/mnt/storage` in addition to our data disk mountpoints of `/mnt/disk1`, `/mnt/disk2` and so on. `/mnt/storage` will be used by MergerFS to 'pool' or 'merge' our data disks.

## Creating fstab entries

Next we need to create an entry in `/etc/fstab`. 

This file tells your OS how, where and which disks to mount. It looks a bit complex but an fstab entry is actually quite simple and breaks down to `<device> <mountpoint> <filesystem> <options> <dump> <fsck>` - [fstab documentation](https://wiki.archlinux.org/index.php/fstab).

Here's what your `/etc/fstab` file might look like with 4 data disks and 1 parity drive for SnapRAID. Note that MergerFS does *not* mount the parity drive, it only mounts `/mnt/disk*`. Remember, MergerFS has *nothing to do* with parity which is what we have SnapRAID for.

```
##/etc/fstab example
/dev/disk/by-id/ata-WDC_WD100EMAZ-00WJTA0_16G0Z7RZ-part1 /mnt/parity1 xfs defaults 0 0
/dev/disk/by-id/ata-WDC_WD100EMAZ-00WJTA0_16G10VZZ-part1 /mnt/disk1   xfs defaults 0 0
/dev/disk/by-id/ata-WDC_WD100EMAZ-00WJTA0_2YHV69AD-part1 /mnt/disk2   xfs defaults 0 0
/dev/disk/by-id/ata-WDC_WD100EMAZ-00WJTA0_2YJ15VJD-part1 /mnt/disk3   xfs defaults 0 0
/dev/disk/by-id/ata-HGST_HDN728080ALE604_R6GPPDTY-part1  /mnt/disk4   xfs defaults 0 0

/mnt/disk* /mnt/storage fuse.mergerfs defaults,nonempty,allow_other,use_ino,cache.files=off,moveonenospc=true,dropcacheonclose=true,minfreespace=200G,fsname=mergerfs 0 0
```

Before you reboot, it's a good idea to check that everything mounted as you hoped. We can use `df -h` to verify this.

```
root@cartman:~# df -h
Filesystem                        Size  Used Avail Use% Mounted on
/dev/sdo2                          59G   22G   34G  39% /
/dev/sdj1                         469G  118G  328G  27% /opt
/dev/sde1                         9.1T  7.1T  2.1T  78% /mnt/disk1
/dev/sdg1                         9.1T  547G  8.6T   6% /mnt/disk2
/dev/sdm1                         9.1T  5.6T  3.6T  62% /mnt/disk3
/dev/sdc1                         7.3T  2.8T  4.6T  38% /mnt/disk4
/dev/sdl1                         9.1T  7.2T  2.0T  79% /mnt/parity1
mergerfs                           34T   24T   10T  69% /mnt/storage
```

If you had any existing files on your data disks they will be visible under `/mnt/storage`.

# SnapRAID installation

## Compilation

## Configuration

## Automating Parity Calculation

# Network File Sharing

## Samba

## NFS

