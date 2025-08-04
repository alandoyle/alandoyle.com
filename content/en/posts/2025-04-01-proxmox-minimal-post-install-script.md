---
title: "Proxmox - Minimal Post Install Script"
description: ""
author:
email:
faicon: fa-solid fa-server
date: 2025-04-01T14:07:19+01:00
publishDate: 2025-04-01T14:07:19+01:00
images: []
draft: false
tags: [proxmox]
summary: For those looking only to set up the bare essentials, on a Proxmox Viretual Environment or Backup Server, this script has you covered.
cover:
    image: "/images/logo/proxmox-full-lockup-color.png"
    alt: "Proxmox"
    imageurl: "https://www.proxmox.com"
    showcredits: false
    relative: false
    hidden: false
---

# Minimal Setup Script

Earlier this year (2025) I’ve [written about a simple bash script](/blog/proxmox-initial-setup/) I wrote to setup a freshly installed Proxmox Virtualization or Backup server. However, I've been asked for a minimal version that just sets up the bare essentials on a Proxmox install without adding any extra software/aliases/etc., so here it is.

The script only makes a few essential changes to the Proxmox server.

  - Enable IOMMU
  - Enable “No subscription” repositories.
  - Disable “Nag popup” in the web UI.
  - Update the CPU to the latest microcode.
  - Update firmware to latest available.

# Installation

To install this script on a freshly installed Proxmox server run the following command as root via ssh.

``` bash
bash -c "$(wget -qLO - https://github.com/alandoyle/proxmox-setup/raw/main/proxmox-minimal-setup)"
```

After a reboot the initial install is complete.

## Note

Only run this script on a *freshly installed* Proxmox server. I've repeated this several times but it's worth repeating again.
