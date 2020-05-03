# buildhelper
helper for building with devtools-alarm on Arch ARM using distcc

## Requirements
* An NFS export on a server for temp space (mounted and exported to `/scratch` in my case) that will also serve as the distcc volunteer.
* Distcc on both the ARM device as well as on the x86_64 volunteer that is also serving up the NFS export.

This software is not generic as-is, you will have to edit it, see below. 

## Setup on the ARM device
1. Copy `build` somewhere in your path like `~/bin/` and make it executable.
2. Edit it replacing the value for the variable `SERVER` to correspond to the ip address of the x86_64 box.
3. Put the two config files in `/usr/share/` and edit them adjusting the `DISTCC_HOSTS` array.
4. Install, configure, and run distcc
5. Make sure you user has sudo rights
6. If planning to build armv6h, create `/var/cache/pacman/pkg6/`

## Setup on the x86_64 volunteer
1. Setup the NFS export which on my machine this is `/scratch` which is also mounted as tmpfs (optional).
2. Install and configure distcc and [distccd-alarm](https://aur.archlinux.org/packages/distccd-alarm-armv7h/) from the AUR. This includes optionally opening up needed ports on your firewall.

## Usage
To create or update the armv7h build root, just run: `sudo build 7` on the ARM device.  Once created, build as normal.

## Example
```
% sudo build 7
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
 --> MAKEFLAGS=-j16 makechrootpkg -C /var/cache/pacman/pkg -r /scratch/armc7

% cd /scratch/mesa
% MAKEFLAGS=-j16 makechrootpkg -C /var/cache/pacman/pkg -r /scratch/armc7
```
