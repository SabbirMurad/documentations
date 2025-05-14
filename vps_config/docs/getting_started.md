# Welcome to MkDocs

# Ubuntu 20.04 LTS (Focal Fossa)

**The long-term support version of Ubuntu Server, including the Ussuri release of OpenStack and support guaranteed until April 2025.**

## Initial System Wide OS Updates

```sh
apt update && apt list --upgradable && apt upgrade -y && apt autoremove -y
```

_Some VPS's locally modified GRUB is not configured for encrypted volume or swap partition, so it's okay to leave the warning as it is or you could simply remove the crypto package by running:_

```sh
apt remove cryptsetup
```