---
title: PVE setup
description: 
published: true
date: 2023-01-29T14:48:50.751Z
tags: 
editor: markdown
dateCreated: 2023-01-29T14:48:49.179Z
---

When pve has the issues with keys and reporting error messages like below

```bash
/etc/pve/local/pve-ssl.key: failed to load local private key (key_file or key) at /usr/share/perl5/PVE/APIServer/AnyEvent.pm line 1899
```

It may be because that the keys are removed unexpectedly. Follow [this link](https://pve.proxmox.com/wiki/Proxmox_SSL_Error_Fixing) to fix it.

For a freshly installed PVE,  apt update may cause errors like:

```bash
Err:4 https://enterprise.proxmox.com/debian/pve bullseye InRelease
  401  Unauthorized [IP: 51.79.159.216 443]
Reading package lists... Done
E: Failed to fetch https://enterprise.proxmox.com/debian/pve/dists/bullseye/InRelease  401  Unauthorized [IP: 51.79.159.216 443]
E: The repository 'https://enterprise.proxmox.com/debian/pve bullseye InRelease' is not signed.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
This is due to the fact that it defaults to an enterprise version. Since we will never subscribe to such a version, we will use no-subscription sources instead. TUNA has the most complete source for PVE.
Navigate to https://mirrors.tuna.tsinghua.edu.cn/help/debian/ for the Debian sources (since PVE is based on Debian). Beforehands, apt install apt-transport-https ca-certificates.
Navigate to https://mirrors.tuna.tsinghua.edu.cn/help/proxmox/ for the PVE-specific source w/o subscription; also remove/backup /etc/apt/sources.list.d/pve-enterprise.list.
```