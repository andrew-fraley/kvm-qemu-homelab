# Linux Guest Setup

Linux guests are straightforward, VirtIO is supported natively, no extra drivers needed. This is just a baseline setup, customize configurations as you see fit for your own personal use, but this will get you a workable Linux guest in operation.

---

## Creating the VM

1. Open virt-manager → **Create a new virtual machine**
2. Select **Local install media (ISO)**
3. Browse and select your Linux ISO (Debian, Ubuntu, Arch, etc.)
4. virt-manager will usually detect the OS automatically
5. Allocate RAM (2–4 GB is plenty for most tasks) and CPUs (2–4)
6. Set disk size — 20–40 GB is fine for a utility VM
7. Check **Customize configuration before install**

---

## Pre-install configuration

### Disk bus → VirtIO

Under **IDE Disk 1**, change the bus to **VirtIO**.

### Network → VirtIO

Under **NIC**, change the device model to **VirtIO**.

### CPU topology (optional but clean)

```
Sockets: 1
Cores: 2
Threads: 2
```

No XML tweaks needed for Linux — the clock offset can stay at `utc` (the default).

---

## Installation

Standard Linux install process. Nothing VM-specific needed.

After install, confirm VirtIO disk and network are working:

```bash
# Should show vda (VirtIO disk, not sda)
lsblk

# Should show virtio_net driver
lspci | grep -i ethernet
```

---

## Post-install hardening (basic)

### Create a non-root user (if not done during install)

```bash
adduser yourname
usermod -aG sudo yourname   # Debian/Ubuntu
# or
usermod -aG wheel yourname  # Arch/RHEL
```

### Update the system

```bash
# Debian/Ubuntu
sudo apt update && sudo apt upgrade -y

# Arch
sudo pacman -Syu
```

### Basic firewall with ufw (Debian/Ubuntu)

```bash
sudo apt install ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh    # if you need SSH access from host
sudo ufw enable
sudo ufw status
```

### SSH from host to VM (optional)

Get the VM's IP:
```bash
ip a
```

From the host:
```bash
ssh yourname@192.168.122.x
```

---

## Snapshots

```bash
# Create
virsh snapshot-create-as --domain debian-guest --name "post-install" --description "Clean install + updates"

# List
virsh snapshot-list debian-guest

# Revert
virsh snapshot-revert debian-guest post-install
```
