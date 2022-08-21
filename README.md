# buildhelper
Helper script for building with devtools-alarm on Arch ARM using distcc

## Requirements
* A x86_64 box that will run distcc and the armv7h or arm8 toolchain (provided by AUR package linked below).
* You must be using graysky's modified [devtools-alarm](https://github.com/graysky2/PKGBUILDs/commits/devtools-alarm) package or else the MAKEFLAGS are not passed to the build script and distccd will run slow as fuck.
* This script requires a few edits to work on your setup, see below.

## Setup on the x86_64 volunteer
1. Install and configure [distcc](https://wiki.archlinux.org/title/Distcc#Volunteers_2) and [distccd-alarm](https://aur.archlinux.org/packages/distccd-alarm-armv8/). This includes optionally opening up needed ports on your firewall.

Although not a requirement, it is highly recommended to provide the build space using NFS so that you are not hitting the RPi's uSD card with tons of I/O.  One way to do this is to setup [NFS](https://wiki.archlinux.org/index.php/NFS) and export one from another machine (could be the one that is serving NFS but that is not a requirement).  If you have enough RAM on the other machine, better yet to use tmpfs instead of bare metal.

## Setup on the ARM device
1. Copy `build` to `~/bin/` and make it executable.
2. Edit it replacing the value for the variable `SERVER` to correspond to the IP address or hostname of the x86_64 box.
3. Edit it replacing the value for the variable `BUILDROOT` to correspond to the directory or partition (NFS export or otherwise) to serve as the build root.
4. Put the two config files in `~/bin/` then edit them adjusting the `DISTCC_HOSTS` array therein to match your setup. Take care not to change the default port values as they correspond to the defaults in [distccd-alarm](https://aur.archlinux.org/packages/distccd-alarm-armv7h/). You can change the number after the slash to the number of cores on the x86_64 volunteer (16 is what I use for my hardware).
5. Install and configure [distcc](https://wiki.archlinux.org/title/Distcc#Client_2)
6. Make sure you user has [sudo](https://wiki.archlinux.org/index.php/Sudo#Using_visudo) rights.

Note that if you are using NFS for your buildroot, one way to manage mounting it is to edit `/etc/fstab` adding an entry for the NFS export like this: `10.9.8.234:/scratch /scratch  nfs  noauto,fg,nofail,x-systemd.automount,x-systemd.mount-timeout=10,_netdev,x-systemd.idle-timeout=1m 0 0`

## Usage
To create or update the build root, just run: `sudo build 7` or `sudo build 8` on the ARM device.  Once created, build as normal.

## Example
```
% sudo build 8
--> Found a chroot so updating it...
:: Synchronizing package databases...
 core is up to date
 extra is up to date
 community is up to date
 alarm is up to date
 aur is up to date
:: Starting full system upgrade...
 there is nothing to do

 --> all setup!
 --> cd /scratch/path/to/PKGBUILD
 --> MAKEFLAGS=-jxx makechrootpkg -r /scratch/armc8

% cd /scratch/mesa
% MAKEFLAGS=-j32 makechrootpkg -r /scratch/armc8
```

In the example above, the x86_64 box has 32 cores.  You should experiment with different values on your own hardware.
