# Windows 10 Guest Setup

Windows is slightly more involved than Linux as a guest because it doesn't ship with VirtIO drivers. Here's the full process, customize this as you see fit after you get a guest functioning, this is a baseline setup for a workable Windows 10 guest. 

---

## Before you start

Download two ISOs:

1. **Windows 10 ISO** — from Microsoft's media creation tool or your preferred source
2. **VirtIO drivers ISO** — https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso

---

## Creating the VM

1. Open virt-manager → **Create a new virtual machine**
2. Select **Local install media (ISO)**
3. Browse and select your Windows 10 ISO
4. Set OS type to **Windows 10** (type "win10" in the search)
5. Allocate RAM and CPUs (don't exceed half your host's resources)
6. Set disk size — 60 GB minimum, 128 GB recommended for actual use
7. Check **Customize configuration before install** before clicking Finish

---

## Pre-install configuration

### XML: reduce CPU overhead

In the XML editor, update the `<clock>` block:

```xml
<clock offset="localtime">
  <timer name="hpet" present="yes"/>
</clock>
```

Click **Apply**.

### CPU topology

Under **CPUs**, set a real topology (not "16 sockets" which is the broken default):

```
Sockets: 1
Cores: 4 (or 8 if your host has plenty)
Threads: 2
```

### Disk bus → VirtIO

Under **IDE Disk 1**, change the disk bus to **VirtIO**.

### Network → VirtIO

Under **NIC**, change the device model to **VirtIO**.

### Add the VirtIO drivers ISO

1. Click **Add Hardware → Storage**
2. Set device type to **CD-ROM**
3. Browse and select the VirtIO drivers ISO you downloaded
4. Click **Finish**

You should now have two CD-ROMs: the Windows ISO and the VirtIO ISO.

---

## Installation

Click **Begin Installation**. Go through the standard Windows setup until you hit the disk selection screen.

At **"Where do you want to install Windows?"** — you'll see no drives listed. This is because Windows doesn't include VirtIO drivers by default.

1. Click **Load driver**
2. Click **OK** to browse
3. Navigate to the VirtIO CD-ROM → `viostor` → `w10` → `amd64`
4. Select the driver and click **Next**

The virtual disk will now appear. Select it and continue with the install.

---

## Post-install

Once Windows is up and running, install the rest of the VirtIO drivers from the ISO for better network and display performance:

1. Open the VirtIO CD-ROM in File Explorer
2. Run `virtio-win-guest-tools.exe`
3. Complete the installer

Then: run Windows Update, configure your user account, and you're done.

---

## Snapshots

Before doing anything destructive, take a snapshot:

```bash
virsh snapshot-create-as --domain windows10 --name "fresh-install" --description "Clean Windows 10"
```

List snapshots:
```bash
virsh snapshot-list windows10
```

Revert to a snapshot:
```bash
virsh snapshot-revert windows10 fresh-install
```
