# buildhelper
helper for building with devtools-alarm on Arch ARM using distcc

## Requirements
* A x86_64 box that will:
	* Run as the distcc volunteer.
	* Run NFS exporting a filesystem for the build.
* Distcc on both the ARM device as well as on the x86_64 volunteer that is also serving up the NFS export.

This script requires a few edits to work on your setup, see below. 

## Setup on the ARM device
1. Copy `build` somewhere in your path like `~/bin/` and make it executable.
2. Edit it replacing the value for the variable `SERVER` to correspond to the IP address or hostname of the x86_64 box.
3. Put the two config files in `/usr/share/` and edit them adjusting the `DISTCC_HOSTS` array therein to match your setup.
4. Install, configure, and run [distcc](https://wiki.archlinux.org/index.php/Distcc).
5. Make sure you user has [sudo](https://wiki.archlinux.org/index.php/Sudo#Using_visudo) rights.

## Setup on the x86_64 volunteer
1. Setup the [NFS](https://wiki.archlinux.org/index.php/NFS) end adjust the `MOUNTPOINT` variable in the script as needed.
2. Install and configure distcc and [distccd-alarm](https://aur.archlinux.org/packages/distccd-alarm-armv7h/). This includes optionally opening up needed ports on your firewall.

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
