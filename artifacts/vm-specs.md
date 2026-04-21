# VM Specs

Reference specs for the VMs in this homelab setup.

---

## Windows 10 Guest

| Setting | Value |
|---|---|
| OS | Windows 10 (any edition) |
| RAM | 8–16 GB |
| vCPUs | 8 (1 socket, 4 cores, 2 threads) |
| Disk | 60–128 GB |
| Disk bus | VirtIO |
| Network model | VirtIO |
| Clock | localtime |
| Extra ISOs | VirtIO Windows drivers ISO (mounted as CD-ROM 2) |

**VirtIO drivers ISO:**

https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso

---

## Linux Guest (Debian/Ubuntu/Arch etc.)

| Setting | Value |
|---|---|
| OS | Any Linux distro |
| RAM | 2–4 GB |
| vCPUs | 2–4 |
| Disk | 20–40 GB |
| Disk bus | VirtIO (natively supported, no extra drivers needed) |
| Network model | VirtIO |
| Clock | utc |
| Extra ISOs | None required |

---

## Host specs (reference)

For personal use as a reference 

| Setting | Value |
|---|---|
| CPU | (your CPU) |
| Total RAM | (your RAM) |
| Storage | (your disk) |
| OS | (your Linux distro) |
| KVM module | Enabled |
