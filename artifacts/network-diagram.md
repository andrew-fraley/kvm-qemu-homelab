# Network Diagram

## Topology

```
┌─────────────────────────────────────────────────────────┐
│                     Linux Host                          │
│                                                         │
│   ┌─────────────┐         ┌─────────────────────────┐   │
│   │  Windows 10 │         │      Linux Guest        │   │
│   │  Guest VM   │         │      (e.g. Debian)      │   │
│   │             │         │                         │   │
│   │ 192.168.122.x         │ 192.168.122.x           │   │
│   └──────┬──────┘         └──────────┬──────────────┘   │
│          │                           │                  │
│          └──────────┬────────────────┘                  │
│                     │                                   │
│              ┌──────▼──────┐                            │
│              │   virbr0    │  ← Virtual bridge          │
│              │ 192.168.122.1│   managed by libvirtd     │
│              └──────┬──────┘                            │
│                     │  NAT                              │
│              ┌──────▼──────┐                            │
│              │  Host NIC   │  (eth0 / wlan0 / etc.)     │
│              └──────┬──────┘                            │
└─────────────────────┼───────────────────────────────────┘
                      │
               ┌──────▼──────┐
               │   Router /  │
               │   Internet  │
               └─────────────┘
```

## Notes

- VMs are assigned IPs in the `192.168.122.0/24` range via DHCP from `dnsmasq`
- The host acts as the NAT gateway; VMs can reach the internet, LAN cannot reach VMs by default
- VM-to-VM traffic stays on the virtual bridge and never hits your physical network
- To expose a VM service to your LAN, set up port forwarding via `iptables` or a bridged network

## Network type: NAT (default)

| Setting | Value |
|---|---|
| Virtual network name | default |
| Bridge | virbr0 |
| Subnet | 192.168.122.0/24 |
| Gateway (host) | 192.168.122.1 |
| DHCP range | 192.168.122.2 – 192.168.122.254 |
| Forwarding | NAT |
