# kvm-qemu-homelab
A practical reference for setting up virtual machine on Linux using KVM, QEMU, and virt-manager. This is useful for simple Linux VMs or Wiindows 10 and prior releases (Windows 11 will be added later) and Windows or Linux servers to host your own lab for various testing, development, training, and other educational purposes (and beyond).


---

## What this is

This repo documents my personal homelab VM environment built on KVM/QEMU with virt-manager as the GUI frontend. It covers installation, configuration, and the setup of both a Windows 10 and a Linux guest VM on a single Linux host.

It's part reference guide, part portfolio artifact, developed to showcase the virtualization process I use, as well as be a reference for me and anyone else interested in setting up their own home lab.

---

## Why it matters

KVM runs with near-native performance because it talks directly to the Linux kernel's virtualization layer. For anyone running Windows software on a Linux desktop, or just wanting a proper homelab without dual-booting, this setup is the right call.

Key advantages over VirtualBox:
- KVM is built into the Linux kernel — no third-party modules
- Significantly better CPU and I/O performance
- virt-manager gives you a clean GUI without giving up terminal access
- Closer to how virtualization works in professional/cloud environments

---

## What it demonstrates

- KVM/QEMU installation and libvirtd service configuration
- User/group permissions for non-root VM management
- Virt-manager GUI setup and XML editing for advanced config
- Windows 10 guest VM setup including VirtIO driver loading
- Linux guest VM setup
- Basic NAT networking with virtual bridge
- Firewall awareness and VM isolation

---

## Repo structure

```
kvm-qemu-homelab/
├── README.md
├── overview.md
├── artifacts/
│   ├── network-diagram.md
│   ├── libvirtd.conf.example
│   └── vm-specs.md
└── examples/
    ├── windows10-setup.md
    └── linux-guest-setup.md
```

---

## Stack

| Component | Role |
|---|---|
| KVM | Kernel-level hypervisor |
| QEMU | Machine emulator, works on top of KVM |
| libvirtd | Daemon that manages VMs |
| virt-manager | GUI frontend |
| VirtIO drivers | Paravirtualized disk/network drivers for Windows guests |
