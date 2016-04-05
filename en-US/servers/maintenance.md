---
name: Server maintenance
---

# Ubuntu server automatic updates

It is annoying updating an Ubuntu server all the time and maintainging security patches on many VMs.

Using [this doc](https://help.ubuntu.com/14.04/serverguide/automatic-updates.html) you can automatically install security updates on your Ubuntu servers.

Gist of it:
```
sudo apt-get install -y unattended-upgrades
```

```
nano /etc/apt/apt.conf.d/50unattended-upgrades
```
Make sure you see these contents at the top of the file:
```
// Automatically upgrade packages from these (origin:archive) pairs
Unattended-Upgrade::Allowed-Origins {
        "${distro_id}:${distro_codename}-security";
//      "${distro_id}:${distro_codename}-updates";
//      "${distro_id}:${distro_codename}-proposed";
//      "${distro_id}:${distro_codename}-backports";
};
```
This will install security updates, not regular program updates.

```
nano /etc/apt/apt.conf.d/10periodic
```
Contents of file should be:
```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";
```
The above configuration updates the package list, downloads, and installs available upgrades every day. The local download archive is cleaned every week.
