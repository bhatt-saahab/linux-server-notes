## Overview

The **Advanced Package Tool (APT)** is the high-level package manager for Debian-based systems such as Ubuntu, Kali Linux, and Raspberry Pi OS. It works on top of `dpkg` to install, upgrade, and remove packages while automatically resolving dependencies.

---

## System Information

### Check System Release Details

```bash
cat /etc/os-release

```

### Check Debian/Ubuntu Codename

```bash
lsb_release -a

```

### Print System Architecture

```bash
dpkg --print-architecture

```

---

## Managing APT Repositories

### Repository Files

Repositories are listed in:

```bash
/etc/apt/sources.list
/etc/apt/sources.list.d/

```

### Edit Repositories

```bash
vim /etc/apt/sources.list

```

### Example Debian 12 (Bookworm) Sources

```bash
deb http://deb.debian.org/debian bookworm main contrib non-free non-free-firmware
deb http://security.debian.org/debian-security bookworm-security main contrib non-free non-free-firmware
deb http://deb.debian.org/debian bookworm-updates main contrib non-free non-free-firmware

```

### Add a New Repository (Example: Kali Rolling)

```bash
echo "deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware" | tee /etc/apt/sources.list.d/kali.list

```

### Fix GPG Signature Errors (Missing Keys)

```bash
apt install debian-archive-keyring

```

### Manually Add a GPG Key

```bash
curl -fsSL https://download.example.com/key.gpg | gpg --dearmor -o /etc/apt/trusted.gpg.d/example.gpg

```

---

## Basic Package Management

### Update Package Lists

```bash
apt update

```

### List Available Upgrades

```bash
apt list --upgradable

```

### Upgrade All Installed Packages

```bash
apt upgrade -y

```

### Perform a Full Upgrade (Handles Dependencies, May Remove Packages)

```bash
apt full-upgrade -y

```

### Install a Package

```bash
apt install package_name -y

```

### Install Multiple Packages

```bash
apt install pkg1 pkg2 pkg3 -y

```

### Remove a Package but Keep Config Files

```bash
apt remove package_name -y
apt remove apache2
dpkg -l | grep apache

```

### Remove a Package and Its Config Files

```bash
apt purge package_name -y
apt purge apache2 -y

```

### Remove Unused Dependencies

```bash
apt autoremove -y

```

### Reinstall a Package

```bash
apt reinstall package_name

```

### Download a Deb File Without Installing

```bash
apt download package_name

```

---

## Searching and Package Information

### Search for a Package

```bash
apt search package_name
apt search python3

```

### Show Details About a Package

```bash
apt show package_name
apt show python3

```

### Show Dependencies of a Package

```bash
apt depends package_name
apt depends python3

```

### Show Reverse Dependencies

```bash
apt rdepends package_name
apt rdepends python3

```

### Show Source Package Information

```bash
apt showsrc package_name
apt showsrc python3

```

### Download Package Source Code

```bash
apt source package_name
apt source python3

```

### View Package Changelog

```bash
apt changelog package_name

```

---

## Cache and Cleanup

### Clean the Entire Package Cache

```bash
apt clean

```

### Remove Only Outdated Cached Files

```bash
apt autoclean

```

### Check Cache Size

```bash
du -sh /var/cache/apt/archives

```

---

## Fixing Common APT Issues

### Fix Broken Dependencies

```bash
apt --fix-broken install

```

### Reconfigure Packages Left in an Interrupted State

```bash
dpkg --configure -a

```

### Force Reinstall Missing Dependencies

```bash
apt-get install -f

```

### Remove APT Locks if Package Manager is Stuck

```bash
sudo rm /var/lib/dpkg/lock
sudo rm /var/lib/dpkg/lock-frontend

```

### Clear Partial Downloads

```bash
sudo rm /var/cache/apt/archives/partial/*

```

---

## Useful APT Tools

### List All Packages

```bash
apt list
apt list | wc -l

```

### List Installed Packages

```bash
apt list --installed
apt list --installed | wc -l

```

### List Upgradable Packages

```bash
apt list --upgradable

```

### Update the File Search Database

```bash
apt-file update

```

### Find Which Package Provides a Specific File

```bash
apt-file search /path/to/file

```

### Show the Version of an Installed Package

```bash
apt policy package_name

```

---

## Advanced Usage

### Simulate an Installation (Dry-Run)

```bash
apt install --simulate package_name

```

### Hold a Package to Prevent Upgrades

```bash
apt-mark hold package_name

```

### Unhold a Package (Allow Upgrades Again)

```bash
apt-mark unhold package_name

```

### List Held Packages

```bash
apt-mark showhold

```

### Pin Package Versions (Via /etc/apt/preferences)

```
Package: firefox
Pin: version 115.*
Pin-Priority: 1001

```

---

## Quick Tips

- Always run `apt update` before installing or upgrading.
- Use `apt full-upgrade` when switching between releases (e.g., Debian 11-12).
- Clean the cache regularly with `apt clean` to free disk space.
- Combine `dpkg -l` with `apt` to troubleshoot dependency issues.

---

## apt-cache and apt-get

The `apt-cache` and `apt-get` commands are legacy but powerful tools in Debian-based systems. They allow searching, querying package metadata, and managing package installation/removal. While `apt` is now the recommended high-level tool, these commands are still widely used in scripts and troubleshooting.

### apt-cache: Query Package Cache

### Search for a Package by Keyword

```bash
apt-cache search package_name
apt-cache search python3

```

### Show Detailed Info About a Package

```bash
apt-cache show package_name
apt-cache show nmap

```

### List Dependencies of a Package

```bash
apt-cache depends package_name
apt-cache depends curl

```

### List Reverse Dependencies (Packages Depending on It)

```bash
apt-cache rdepends package_name
apt-cache rdepends libc6

```

### Show Source Package Details

```bash
apt-cache showsrc package_name
apt-cache showsrc openssh

```

### List All Package Names in the Cache

```bash
apt-cache pkgnames

```

### Show Installed and Available Versions with Priority

```bash
apt-cache policy package_name
apt-cache policy nginx

```

### apt-get: Package Management

### Update Package Lists

```bash
apt-get update

```

### Upgrade Installed Packages

```bash
apt-get upgrade -y

```

### Perform a Full Upgrade (Handles Deps, May Remove Packages)

```bash
apt-get full-upgrade -y

```

### Install a Package

```bash
apt-get install package_name -y
apt-get install apache2

```

### Remove a Package (Keep Config)

```bash
apt-get remove package_name -y
apt-get remove apache2

```

### Remove a Package with Config

```bash
apt-get purge package_name -y
apt-get purge apache2

```

### Remove Orphaned Dependencies

```bash
apt-get autoremove -y

```

### Download a Deb File Without Installing

```bash
apt-get download package_name
apt-get download nmap

```

### Get Package Source Code

```bash
apt-get source package_name
apt-get source openssl

```

### Show Changelog for a Package

```bash
apt-get changelog package_name
apt-get changelog bash

```

### Verify Package Database

```bash
apt-get check

```

### Clear Cached .deb Files

```bash
apt-get clean

```

---

## Fixing Issues with apt-get

### Fix Broken Dependencies

```bash
apt-get install -f

```

### Reconfigure Packages

```bash
dpkg --configure -a

```

### Remove Lock Files (If apt is Stuck)

```bash
rm /var/lib/dpkg/lock
rm /var/lib/dpkg/lock-frontend

```

### Clear Partial Downloads

```bash
rm /var/cache/apt/archives/partial/*

```

---

## Quick Tips for apt-cache and apt-get

- `apt-get` is script-friendly; use it when writing automation.
- Always run `apt-get update` before installing or upgrading.
- Use `apt-get autoremove` after uninstalling large packages to free space.
- For human-friendly usage, prefer `apt`, but for precise scripting, use `apt-get`.
