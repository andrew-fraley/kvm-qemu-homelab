# Overview

## Prerequisites

- A Linux host with a CPU that supports hardware virtualization (Intel VT-x or AMD-V)
- KVM kernel modules available (most modern distros ship with these enabled)
- You can verify: `grep -E 'vmx|svm' /proc/cpuinfo` — if you see output, you're good

---

## 1. Installation

Install the required packages. Package names may vary slightly by distro.

**Arch / Artix / Manjaro:**
```bash
sudo pacman -S qemu virt-manager virt-viewer dnsmasq vde2 bridge-utils openbsd-netcat libguestfs
```

**Ubuntu / Debian:**
```bash
sudo apt install qemu-kvm virt-manager virtinst libvirt-daemon-system libvirt-clients bridge-utils
```

**Artix-specific note:** libvirt is split into the base package and an init-system package. If you're using OpenRC:
```bash
sudo pacman -S libvirt-openrc
```

---

## 2. Start and enable the libvirtd service

**systemd (Ubuntu, Fedora, most distros):**
```bash
sudo systemctl enable --now libvirtd
```

**OpenRC (Artix):**
```bash
sudo rc-service libvirtd start
sudo rc-update add libvirtd default
```

---

## 3. Configure libvirtd for non-root access

Edit `/etc/libvirt/libvirtd.conf`:

```
unix_sock_group = "libvirt"
unix_sock_ro_perms = "0777"
unix_sock_rw_perms = "0770"
```

Then add your user to the `libvirt` group:

```bash
sudo usermod -aG libvirt $USER
```

Log out and back in (or reboot) to apply the group change. Then restart libvirtd:

```bash
# systemd
sudo systemctl restart libvirtd

# OpenRC
sudo rc-service libvirtd restart
```

---

## 4. Configure virt-manager

Open virt-manager and:

1. Connect to `QEMU/KVM` — click **Connect** if not already connected
2. Go to **Edit → Preferences** and enable **XML editing**
3. Go to **Edit → Connection Details** — confirm your virtual network is active with NAT forwarding

If the virtual network is not active, your VMs won't have internet access.

---

## 5. Creating a VM (general flow)

1. Click **Create a new virtual machine**
2. Select **Local install media (ISO)**
3. Browse and select your ISO
4. Set RAM and CPU — rule of thumb: don't give the VM more than half of what your host has (this is a flexible rule, but plan accordingly)
5. Set disk size — What I do is typically 40GB or more for Windows, 20–40GB is fine for most Linux guests (you can do smaller disk sizes for these if you are just testing workgroups, AD, etc. and not performing tasks or heavily workloads on the guests)
6. Check **Customize configuration before install**
NOTE: Most of your configurations are flexible and should be tested before committing to a full workable lab scenario, always test and plan accordingly depending on your end goal for each guest and lab scenario.

### XML tweak (reduce CPU overhead)

In the XML editor, find the `<clock>` section and set:
```xml
<clock offset="localtime">
  <timer name="hpet" present="yes"/>
</clock>
```
This reduces idle CPU usage inside the VM.

### CPU topology

Under **CPUs**, set a sane topology rather than the default (which often sets everything as sockets):

| Setting | Value |
|---|---|
| Sockets | 1 |
| Cores | 4–8 (half your physical cores) |
| Threads | 2 |

### Disk and network bus

Change both the **disk bus** and **network model** to `VirtIO` for significantly better I/O performance. This applies to Linux guests natively. For Windows, you'll need to load drivers first (see `examples/windows10-setup.md`).

---

## 6. Users and permissions

| What | How |
|---|---|
| Run VMs without sudo | Add user to `libvirt` group |
| Access VM storage | Disk images live in `/var/lib/libvirt/images/` by default |
| Manage via CLI | `virsh` command — e.g. `virsh list --all` |

---

## 7. Firewall considerations

KVM/QEMU with NAT networking creates a virtual bridge (`virbr0` by default). VMs sit behind this bridge and are isolated from your LAN by default.

Key points:
- VMs can reach the internet (outbound) via NAT
- Your LAN cannot reach VMs unless you forward ports or set up a bridged network
- `iptables` rules are managed automatically by libvirtd
- If you run a host firewall (ufw, firewalld, nftables), make sure it doesn't block the `virbr0` interface

Check the active virtual network:
```bash
virsh net-list --all
virsh net-info default
```

---

## 8. Keeping VMs updated

There's no magic here, update VMs the same way you'd update any OS:

- **Windows 10 guest:** Windows Update inside the VM
- **Linux guest:** run your package manager (`apt update && apt upgrade`, `pacman -Syu`, etc.)

One practical tip: snapshot your VM before major updates.

```bash
virsh snapshot-create-as --domain <vm-name> --name "pre-update" --description "Before system update"
```
