# Arch Linux Notes
My Personal Notes on Arch Linux. These notes are specifically for my laptop, **Asus TUF A16 Gaming Laptop FA617NT**. However, there are going to be many general tips that can be applied to any Arch Linux installation. Efforts will be taken to link back to source materials when possible and avoid redundancy. 

## Table of Contents
1. [Installation](#installation)
2. [Post Installation](#postInstallation)
3. [Changes to Kernel Boot Options](#changesToKernelBootOptions)
4. [Tips on Package Management](#tipsOnPackageManagement)

<a name="installation"></a>

## Installation
[Arch Install Guide](https://wiki.archlinux.org/title/Installation_guide)

<a name="postInstallation"></a>

## Post Installation
[Asus Linux Arch Install Guide](https://asus-linux.org/guides/arch-guide/)

[Asus Linux FAQ](https://asus-linux.org/faq/)

<a name="changesToKernelBootOptions"></a>

## Changes to Kernel Boot Options
### Fixing Refresh Rate and Graphics Artifacting
Add the following flags to the options line in the `/boot/loader/entries/linux.conf` file. The specific .conf file name will be different for each kernel.

`amdgpu.dcdebugmask=0x10 amdgpu.dpm=1 amdgpu.ppfeaturemask=0xffffffff amdgpu.dc=1 amdgpu.si_support=1 amdgpu.cik_support=1`

_**Note:** It is unclear if all of these options are nescessary to resolve Refresh Rate issues and Graphics Artifacting. Issue was posted to Arch BBS [here](https://bbs.archlinux.org/viewtopic.php?pid=2204967#p2204967)_

<a name="tipsOnPackageManagement"></a>

## Tips on Package Management
* Use Flatpacks whenever possible to containerize dependencies and simplify uninstall. 

* Use packages from official Arch repos when available.

* If packages are not available from Arch repos, then use the AUR.

* Keep the system clean and free of orphaned pacakges. 

### AUR Helpers
Prefered AUR help is `paru`. `paru`, by default, makes the user review the PKGBUILD file to review contents to ensure there are no malicious activities in the PKGBUILD.

_**Note:** Before using Paru or another AUR Helper (Such as yay), it is important to learn how to build and install AUR packages on your own without the use of an AUR helper. Refer to [Arch Wiki article](https://wiki.archlinux.org/title/Arch_User_Repository)._