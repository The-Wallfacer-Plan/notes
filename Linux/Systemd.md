---
title: Systemd
description: 
published: true
date: 2023-01-31T16:16:06.572Z
tags: 
editor: markdown
dateCreated: 2023-01-31T15:02:07.851Z
---

## [systemd vs SysVinit](https://www.tecmint.com/systemd-replaces-init-in-linux/)


## Systemctl
- `systemctl --failed`
- `systemctl start/restart/reload/stop unit`
- `systemctl disable/enable/reenable unit`
- `systemctl mask/unmask unit`
- `systemctl status unit`
- `systemctl daemon-reload`
- `systemctl cat/edit unit`
- `systemctl is-active/is-enabled/is-failed unit`
- `systemctl show-environment`
- `systemctl list-timers/list-jobs/list-sockets/list-timers/list-unit-files/list-units`
- `systemctl list-dependencies NetworkManager.service`


## Journalctl
- `journalctl -ex -b`
- `journalctl --since=today -f`
- `journalctl --vacuum-time=5m`
- `journalctl --vacuum-size=10M`

## hostnamectl

## timedatectl
