# Phase 1: Basic Networking Setup

## Overview

Phase 1 establishes the foundational network topology for the homelab. This means getting a managed router (MikroTik hEX) between the ISP modem and all lab devices, confirming WAN connectivity, and validating that all hosts can reach each other and the internet via the hEX LAN bridge. Everything built in later phases (VLANs, firewall rules, VPN, etc.) depends on this working correctly.

---

## Network Topology

```
ISP Modem (192.168.1.x)
    │
    └── hEX ether1 (WAN) — receives 192.168.1.168
         │
         hEX LAN bridge (192.168.88.x)
         ├── ether2 → Proxmox Node 1
         ├── ether3 → Proxmox Node 2
         ├── ether4 → Proxmox Node 3
         └── ether5 → WiFi AP (wAP)
```

> See `network-diagram.png` for the visual diagram.

---

## Prerequisites

| Item | Notes |
|------|-------|
| MikroTik hEX router (RB750Gr3 or similar) | 5-port GbE, PoE out on port 5 |
| ISP modem/router | Must provide DHCP on its LAN (e.g., 192.168.1.x) |
| Proxmox nodes (×3) | Any x86 hardware with at least one NIC |
| MikroTik wAP or other AP | Connected to hEX port 5 |
| Winbox | MikroTik management GUI — [download here](https://mikrotik.com/download) |
| Ethernet cables | Cat5e or better for each device connection |

---

## Step-by-Step Setup

### Step 1 — Connect hEX ether1 to ISP Modem

1. Run an ethernet cable from a LAN port on the ISP modem to **ether1** on the hEX.
2. Power on the hEX.
3. The hEX ether1 interface should receive a DHCP lease from the modem (e.g., `192.168.1.168`).

> **Note:** This creates a double-NAT scenario (modem NATs to 192.168.1.x, hEX NATs to 192.168.88.x). This is intentional for Phase 1. Bridge mode on the modem is an optional optimization — see `notes.md`.

### Step 2 — Connect Devices to hEX Ports 2–5

| hEX Port | Device |
|----------|--------|
| ether2 | Proxmox Node 1 |
| ether3 | Proxmox Node 2 |
| ether4 | Proxmox Node 3 |
| ether5 | WiFi AP (wAP) |

Run ethernet from each device's NIC to the corresponding hEX port.

### Step 3 — Verify WAN Connection

1. Open **Winbox** and connect to the hEX (use the MAC address tab if you don't know the IP yet).
2. Navigate to **Interfaces** — confirm ether1 shows as running (green indicator).
3. Navigate to **IP → Addresses** — confirm ether1 has an address in the `192.168.1.x` range assigned by the modem.

### Step 4 — Verify LAN Bridge

1. In Winbox, navigate to **Bridge** — confirm the bridge interface exists and includes ether2–ether5.
2. Navigate to **IP → Addresses** — confirm the bridge interface has an address in the `192.168.88.x` range (default hEX LAN is `192.168.88.1/24`).
3. Navigate to **IP → DHCP Server** — confirm a DHCP server is active on the bridge and the pool covers `192.168.88.x`.

### Step 5 — Test Device Connectivity

On each Proxmox node, verify:

```bash
# Check assigned IP (should be 192.168.88.x)
ip addr show

# Ping the hEX gateway
ping 192.168.88.1

# Ping an external address
ping 8.8.8.8
```

From Winbox, navigate to **Tools → Ping** and test pinging `8.8.8.8` to confirm the hEX itself has internet access.

---

## Verification Checklist

- [ ] hEX ether1 shows IP `192.168.1.x` in Winbox → IP → Addresses
- [ ] hEX LAN bridge shows IP `192.168.88.1/24`
- [ ] DHCP server is active and serving the `192.168.88.x` pool
- [ ] All Proxmox nodes received a `192.168.88.x` address via DHCP
- [ ] Proxmox nodes can ping `192.168.88.1` (gateway)
- [ ] Proxmox nodes can ping `8.8.8.8` (internet)
- [ ] Winbox can reach hEX management interface
- [ ] WiFi AP is reachable and clients can get internet

---

## Screenshots to Take

See `screenshots/README.md` for the full list of required screenshots and the order to capture them.

---

## What's Next

**Phase 2: VLANs on Cisco Switch**

Phase 2 introduces network segmentation using VLANs. A managed Cisco switch will be added between the hEX and the Proxmox nodes. VLANs will isolate traffic by function (e.g., management, storage, VM traffic). The flat `192.168.88.x` network will be split into separate subnets per VLAN.

---

## Files in This Folder

| File/Folder | Purpose |
|-------------|---------|
| `README.md` | This setup guide |
| `notes.md` | Learning reflections and key concepts |
| `configs/` | Router backup files and export notes |
| `screenshots/` | Screenshot checklist and captured images |
| `network-diagram.png` | Visual topology diagram |
