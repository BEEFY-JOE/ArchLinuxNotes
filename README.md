# ArchLinuxNotes
My Personal Notes on Arch Linux. This installation guide is for Asus TUF A16 Gaming Laptop **FA617NT**. Efforts will be taken to link back to source materials when possible and avoid redundancy. 

## Installation
[Arch Install Guide](https://wiki.archlinux.org/title/Installation_guide)

## Post Installation
[Asus Linux Arch Install Guide](https://asus-linux.org/guides/arch-guide/)

[Asus Linux FAQ](https://asus-linux.org/faq/)

## Changes to Kernel Boot Options
### Fixing Refresh Rate and Graphics Artifacting
Add the following flags to the options line in the `/boot/loader/entries/linux.conf` file. The specific .conf file name will be different for each kernel. 

`amdgpu.dcdebugmask=0x10 amdgpu.dpm=1 amdgpu.ppfeaturemask=0xffffffff amdgpu.dc=1 amdgpu.si_support=1 amdgpu.cik_support=1`

_**Note:** It is unclear if all of these options are nescessary to resolve Refresh Rate issues and Graphics Artifacting. Issue was posted to Arch BBS [here](https://bbs.archlinux.org/viewtopic.php?pid=2204967#p2204967)_

