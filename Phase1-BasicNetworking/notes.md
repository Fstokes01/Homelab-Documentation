# Phase 1 — Learning Notes & Reflections

## What I Learned

### Double NAT

<!-- Fill in: What double NAT means in your setup, why it happens (ISP modem + hEX both doing NAT), and what symptoms or limitations you noticed (if any). -->

### Bridge Mode on the ISP Modem

<!-- Fill in: What bridge/passthrough mode does, whether your ISP modem supports it, and whether you chose to configure it or leave double NAT in place. -->

---

## Key Concepts

### WAN vs. LAN

<!-- Fill in: In your own words, explain the difference between the WAN side (ether1, 192.168.1.x) and the LAN side (bridge, 192.168.88.x) of the hEX. -->

### DHCP

<!-- Fill in: How DHCP works, which device is the DHCP server on each segment (modem for 192.168.1.x, hEX for 192.168.88.x), and what happens when a device connects. -->

### Network Segmentation Foundation

<!-- Fill in: Why a flat network (everything on one subnet) is acceptable for Phase 1 but needs to be replaced with VLANs in Phase 2. -->

---

## How This Applies to Enterprise Networking

<!-- Fill in: Parallels between this homelab setup and real enterprise designs — edge routers, LAN segments, managed switches, DMZs, etc. -->

---

## Troubleshooting Tips Discovered

<!-- Fill in: Any issues you hit during Phase 1 and how you resolved them. Examples:
- Winbox couldn't find hEX → solution was to use MAC address discovery tab
- DHCP not handing out IPs → cause and fix
- Double NAT blocking something → workaround
-->

---

## Why This Foundation Matters for Security

<!-- Fill in: How having a dedicated router (vs. relying solely on the ISP modem) improves your security posture, what control you now have over traffic, and how Phase 2 VLANs will build on this. -->

---

## Resources

<!-- List any guides, MikroTik docs, or videos that helped you during Phase 1. -->

- [MikroTik hEX Quick Guide](https://help.mikrotik.com/docs/display/UM/hEX)
- [MikroTik First Time Configuration](https://help.mikrotik.com/docs/display/ROS/First+Time+Configuration)
- [Winbox Download](https://mikrotik.com/download)
