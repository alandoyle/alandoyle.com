---
title: "Proxmox - Upgrade to latest version"
description: ""
author:
email:
faicon: fa-solid fa-server
date: 2025-08-08T23:21:24+01:00
publishDate: 2025-08-08T23:21:24+01:00
images: []
draft: false
ShowToc: true
tags: [proxmox]
summary: Proxmox Virtual Environment 9 and Proxmox Backup Server 4 have just arrived. Let's upgrade our PVE 8 and PBS 3 servers to the latest version.
cover:
    image: "/images/logo/proxmox-full-lockup-color.png"
    alt: "Proxmox"
    imageurl: "https://www.proxmox.com"
    showcredits: false
    relative: false
    hidden: false
---

# Background

On **August 05, 2025** – Leading open-source server solutions provider Proxmox Server Solutions GmbH (henceforth "Proxmox"), celebrating its 20th year of innovation, announced the release of Proxmox Virtual Environment (VE) 9.0. The main highlight of this update is a modernized core built upon Debian 13 “Trixie”, ensuring a robust foundation for the platform.

While on **August 6, 2025** – Proxmox announced the release of Proxmox Backup Server (PBS) 4.0. The main highlight of this update is a modernized core built upon Debian 13 “Trixie”, just like Proxmox Virtual Environment (VE) 9.0, as well as expanding its backup options by introducing native support for S3-compatible object storage.

The release post for Proxmox Virtual Environment (VE) 9.0 can be found [here](https://www.proxmox.com/en/about/company-details/press-releases/proxmox-virtual-environment-9-0) on the Proxmox website, while the release post for Proxmox Backup Server 4.0 can also be found [here](https://www.proxmox.com/en/about/company-details/press-releases/proxmox-backup-server-4-0) on the Proxmox website.

---

# Preparation

![Hard drive](/images/blog/benjamin-lehman-GNyjCePVRs8-unsplash_cropped.jpg)

It goes without saying but before embarking on the upgrade you should read the section on ["Known issues"](https://pve.proxmox.com/wiki/Upgrade_from_8_to_9#Known_Upgrade_Issues) on the Proxmox Wiki page for upgrading Proxmox 8 to 9.

Once you are satisified that you can safely proceed with the upgrade you _MUST_ **BACKUP** all LXC containers and KVM Virtual Machines on the Proxmox node being upgraded.

{{< error title="BACKUP! BACKUP! BACKUP!" >}}
YOU HAVE BEEN WARNED!
{{< /error >}}

If you don't **BACKUP** and the upgrade fails then you're in for a _very_ bad time.

---

# Step 1: Update the current Node

To upgrade to the latest "Trixie" based versions of Proxmox you _MUST_ update your Node to the current latest version. This is very simple and can be achieved by running the following commands from a Shell as **root**

```bash
/usr/bin/apt update
/usr/bin/apt dist-upgrade -y
/usr/bin/apt autoremove -y
```

This may take a while depending on how often you update your Proxmox Nodes.

---

# Step 2: Update _apt_ to new repositories

Proxmox Virtual Environment (VE) 9 and Proxmox Backup Server (PBS) 4 both move the _apt_ configuration from the old-style .list format to the new [deb822](https://repolib.readthedocs.io/en/latest/deb822-format.html) format so we need to create some new .sources files for the upgrade.

## Common Debian Repository

To create the new Debian repository _source_ file simply run the following command as root...

```bash
cat > /etc/apt/sources.list.d/debian.sources << EOF
Types: deb
URIs: http://deb.debian.org/debian/
Suites: trixie trixie-updates
Components: main contrib non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg

Types: deb
URIs: http://security.debian.org/debian-security/
Suites: trixie-security
Components: main contrib non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg
EOF
```

## Create the Enterprise repositories

To enable the Enterprise Repositories file simply run the following commands as root...

```bash
# Remove the # from ONE of the following lines depending on the Node type.
#PROXMOX_TYPE=pve # Proxmox Virtual Environment
#PROXMOX_TYPE=pbs # Proxmox Backup Server

cat > /etc/apt/sources.list.d/${PROXMOX_TYPE}-enterprise.sources << EOF
Types: deb
URIs: https://enterprise.proxmox.com/debian/${PROXMOX_TYPE}
Suites: trixie
Components: ${PROXMOX_TYPE}-enterprise
Signed-By: /usr/share/keyrings/proxmox-archive-keyring.gpg
EOF

cat > /etc/apt/sources.list.d/ceph.sources << EOF
Types: deb
URIs: https://enterprise.proxmox.com/debian/ceph-squid
Suites: trixie
Components: enterprise
Signed-By: /usr/share/keyrings/proxmox-archive-keyring.gpg
EOF
```

## "No subscription"" Users AKA Homelabbers

There are a few extra steps required in order to enable the "No Subscription" repositories and disable the Enterprise repositories.

These changes can be done simply by running the following commands as root...

```bash
# Remove the # from ONE of the following lines depending on the Node type.
#PROXMOX_TYPE=pve # Proxmox Virtual Environment
#PROXMOX_TYPE=pbs # Proxmox Backup Server

echo "Enabled: false" >> /etc/apt/sources.list.d/${PROXMOX_TYPE}-enterprise.sources
echo "Enabled: false" >> /etc/apt/sources.list.d/ceph.sources

cat > /etc/apt/sources.list.d/proxmox.sources << EOF
Types: deb
URIs: http://download.proxmox.com/debian/${PROXMOX_TYPE}
Suites: trixie
Components: ${PROXMOX_TYPE}-no-subscription
Signed-By: /usr/share/keyrings/proxmox-archive-keyring.gpg
EOF

cat > /etc/apt/sources.list.d/ceph-no-sub.sources << EOF
Types: deb
URIs: http://download.proxmox.com/debian/ceph-squid
Suites: trixie
Components: no-subscription
Signed-By: /usr/share/keyrings/proxmox-archive-keyring.gpg
EOF
```

## Remove old repositories.

We now need to remove the old "Bookworm" repositores in order to upgrade safely.

This can be done simply by running the following commands as root...

```bash
> /etc/apt/sources.list
rm /etc/apt/sources.list.d/*.list
```

> NOTE: This will also remove any custom repositories added to the system.
> Custom repositories should only bve added _AFTER_ the upgrade and a clean reboot.

# Step 3: Update the current Node

With all that preperation complete we can noww upgrade to the latest "Trixie" based versions of Proxmox. To do this simply run the following commands from a Shell as **root**

```bash
/usr/bin/apt update
/usr/bin/apt dist-upgrade -y
/usr/bin/apt autoremove -y
```

This _WILL__ take a long timne to run. Several questions will be asked along the way. I'll leave it to your own judgement as to how to answer thjose questions, personally for any _configuration_ file requests I leave the current configuration file intactas this tends to prevent breakages.

---

# Conclusion

Overall the process is really simple. I've updated all 3 of my Proxmox Virtual Environment Nodes from 8.4 to 9.0 without issue. I also created a simple script to automate the process as much as possible.

To run this script simply run the following command as root...

```bash
bash -c "$(wget -qLO - https://github.com/alandoyle/proxmox-setup/raw/main/upgrade-proxmox)"
```
