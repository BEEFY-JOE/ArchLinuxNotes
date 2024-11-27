# Arch Linux Notes
My Personal Notes on Arch Linux. These notes are specifically for my laptop, **Asus TUF A16 Gaming Laptop FA617NT**. However, there are going to be many general tips that can be applied to any Arch Linux installation. Efforts will be taken to link back to source materials when possible and avoid redundancy. 

## Table of Contents
1. [Installation](#installation)
2. [Post Installation](#postInstallation)
3. [Changes to Kernel Boot Options](#changesToKernelBootOptions)
4. [Tips on Package Management](#tipsOnPackageManagement)
    1. [AUR Helpers](#aurHelpers)
    2. [Keeping the System Clean and Free of Orphaned Packages](#keepSystemClean)
    3. [Cleaning Up Pacman/Paru Cache and AUR Clones](#cleanUpPacmanParuCache)
    4. [Rolling Back Packages](#rollBackPackages)
5. [Environment Variable Changes](#envVars)
6. [Fixing Wifi Latency Issues](#wifiLatency)
7. [supergfxctl Graphics Switching Fix](#supergfxctl)

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
If using systemd-boot add the following flags to the options line in the `/boot/loader/entries/linux.conf` file. The specific .conf file name will be different for each kernel.

`amdgpu.dcdebugmask=0x10 amdgpu.dpm=1 amdgpu.ppfeaturemask=0xffffffff amdgpu.dc=1 amdgpu.si_support=1 amdgpu.cik_support=1`

_**Note:** It is unclear if all of these options are nescessary to resolve Refresh Rate issues and Graphics Artifacting. Issue was posted to Arch BBS [here](https://bbs.archlinux.org/viewtopic.php?pid=2204967#p2204967)_

<a name="tipsOnPackageManagement"></a>

## Tips on Package Management
* Use Flatpacks whenever possible to containerize dependencies and simplify uninstall. 

* Use packages from official Arch repos when available.

* If packages are not available from Arch repos, then use the AUR.

* Keep the system clean and free of orphaned pacakges. 

<a name="aurHelpers"></a>

### AUR Helpers
Prefered AUR helper is `paru`. Paru, by default, makes the user review the PKGBUILD file to review contents to ensure there are no malicious activities in the PKGBUILD.

_**Note:** Before using Paru or another AUR Helper (Such as yay), it is important to learn how to build and install AUR packages on your own without the use of an AUR helper. Refer to [Arch Wiki article](https://wiki.archlinux.org/title/Arch_User_Repository)._

<a name="keepSystemClean"></a>

### Keeping the System Clean and Free of Orphaned Packages
It is important to keep the system clean of extraneous packages that are uneeded. Only install packages and dependencies that are absolutely needed. When uninstalling packages that were sourced from the official Arch repos or the AUR we can ensure that we are removing packages cleanly by using the following flags with `pacman` or `paru`. `-Rs`. Refer to [pacman man file](https://man.archlinux.org/man/pacman.8.en) for what these flags do.

**Example**
```
paru -Rs package-name
```

If you already uninstalled packages and forgot to use the `-Rs` flags then you can use the following command to remove unused dependencies:
```
paru -Qdtq | paru -Rns -
``` 
Again refer to the [pacman man file](https://man.archlinux.org/man/pacman.8.en) for what these flags do.

<a name="cleanUpPacmanParuCache"></a>

### Cleaning Up Pacman/Paru Cache and AUR Clones
When installing packages from the AUR with `paru`, these clone the AUR repo for that package do your local computer. These files are usually not needed after installation of the AUR package. Use the following command to cleanup the pacman cache, paru cache, and the paru AUR package clones (clones are located in `~/.cache/paru/clone/` by default). Accepting all with `y`.

_**Note:** Use this command carefully, and understand what it does before proceeding. You may wish to keep cached packages from the official repos or AUR in order to rollback to a previous release if something breaks on an update._
```
paru -Scc
```

<a name="rollBackPackages"></a>

### Rolling Back Packages
Unfinished: _this section needs to be filled out once information is re-learned._

<a name="envVars"></a>

## Environment Variable Changes
### Changes to Kwin for Hybrid Graphics Gaming on External Monitors
When using KDE Desktop with my particular setup (3 monitors, one plugged into the laptop directly with HDMI, and the other connected via USB4 port and a DisplayLink dock) there is odd behavior with games in Fullscreen mode played on the external monitor on the HDMI. If you take focus away from the window by moving the mouse outside of the window, no frames will be generated or will only be generated momentarily and will persist until the program is restarted completely.

My old work around for this issue was to turn on the laptop's MUX switch using `supergfxctl -m AsusMuxDgpu` and then reboot. However, it is annoying to reboot. 

A better work around for this problem is to make the following change to your `/etc/environment` file so that Kwin prioritizes the dGPU for rendering over the other cards.
```
sudo nano /etc/environment
```
Then add the following line, save the file, and the reboot the computer;
```
KWIN_DRM_NO_DIRECT_SCANOUT=1
```
Now the issue no longer occurs. Issue was documented in [this gitlab issue report.](https://gitlab.freedesktop.org/drm/amd/-/issues/2075) If this work around helped you, please take the time to report the real issue in the Gitlab issues thread so that it can maybe get patched one day.

<a name="wifiLatency"></a>
## Fixing Wifi Latency Issues
### Disable NetworkManager Wifi Powersave
Issue is documented and on [this Github thread](https://gist.github.com/jcberthon/ea8cfe278998968ba7c5a95344bc8b55#file-wifi-powersave-off-conf) and several other places on the internet.

Disable Wifi Powersaving by creating `wifi-powersave-off.conf` file inside the `/etc/NetworkManager/conf.d` directory.
Inside the file you just created, enter the following;
```
# File to be place under /etc/NetworkManager/conf.d
[connection]
# Values are 0 (use default), 1 (ignore/don't touch), 2 (disable) or 3 (enable).
wifi.powersave = 2
```
Then run, `sudo systemctl restart NetworkManager` in your shell.

Voila! Wireless latency spikes and performance are now vastly improved. 

<a name="supergfxctl"></a>
## supergfxctl Graphics Switching Fix

There appears to be an issue with the latest version of `supergfxctl` (v5.2.4) from the asus-linux g14 repository. When this version is installed the system journal is spammed with error messages about dGPU not detected, and the ability to change to Integrated graphics only mode is not possible. To fix the issue rollback to working version 5.2.1 by doing the following;

### Disable and stop current supergfxctl service
```
sudo systemctl stop supergfxctl.service
sudo systemctl disable supergfxctl.service
```

### Uninstall g14 repository supergfxctl 5.2.4
`yay -Rns supergfxctl`

### Build and Install 5.2.1 from the supergfxctl source on Gitlab
```
cd ~/prefered/directory/forGitRepoClones
git clone https://gitlab.com/asus-linux/supergfxctl.git
cd supergfxctl
git checkout tags/5.2.1
make && sudo make install
sudo systemctl enable supergfxctl.service --now
```

This should resolve the ability to switch to Integrated gpu only, and all other functionality of the application should work now as expected. In addition system journal should no longer be spammed with `ERROR: get_runtime_status: Could not find dGPU` errors, and should be able to detect the dGPU properly. 
This issue has been reported in [Issue #144](https://gitlab.com/asus-linux/supergfxctl/-/issues/144) on the asus-linx supergfxctl GitLab. If this helped you, please contribute to the issue on the GitLab so this bug can be identified and eventually fixed. 