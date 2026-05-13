# Phase 2: VLANs on Cisco Switch

## Overview

Phase 2 introduces a managed Cisco switch between the router and lab nodes, establishes VLAN 10 for the lab network, configures the switch management interface, and sets a trunk uplink to the MikroTik router for current and future VLAN traffic. This phase confirms all core lab systems can communicate across VLAN 10 and remain reachable for Proxmox management.

---

## Network Topology

```
MikroTik Router (VLAN 10 gateway: 192.168.88.1)
    │
    └── Cisco Switch Fa0/1 (trunk)
          ├── Fa0/2 → Management PC (VLAN 10)
          ├── Fa0/3 → Proxmox Node 1 (VLAN 10)
          ├── Fa0/4 → Proxmox Node 2 (VLAN 10)
          └── Fa0/5 → Proxmox Node 3 (VLAN 10)

Cisco Switch SVI (VLAN 10): 192.168.88.2/24
```

---

## Prerequisites

| Item | Notes |
|------|-------|
| Cisco managed switch | Supports VLANs, access ports, and trunking |
| MikroTik router | VLAN 10 gateway at `192.168.88.1` |
| Management PC | Static IP in `192.168.88.x` |
| Proxmox nodes (×3) | Static IPs in `192.168.88.x` |
| Ethernet cables | Cabling from router and all lab devices to switch |

---

## Step-by-Step Setup

### Step 1 — Reset VLAN State and Prepare the Switch

1. Deleted old VLAN database from switch flash: `delete flash:vlan.dat`.
2. Rebooted the Cisco switch.
3. Verified previous VLANs and assignments were removed.

### Step 2 — Create VLAN 10 (`LABS`)

1. Created VLAN `10`.
2. Assigned name: `LABS`.
3. Verified with `show vlan brief`.

### Step 3 — Assign Access Ports for Lab Devices

Configured `Fa0/2` through `Fa0/5` as access ports in VLAN 10:

| Switch Port | Device |
|-------------|--------|
| Fa0/2 | Management PC |
| Fa0/3 | Proxmox Node 1 |
| Fa0/4 | Proxmox Node 2 |
| Fa0/5 | Proxmox Node 3 |

Validated VLAN membership with `show vlan brief`.

### Step 4 — Configure Switch Management Interface

1. Set switch management interface to VLAN 10 (SVI).
2. Assigned switch management IP: `192.168.88.2/24`.
3. Set default gateway: `192.168.88.1`.

### Step 5 — Configure Switch-to-Router Trunk Link

1. Set `Fa0/1` as a trunk port toward the MikroTik router.
2. Ensured VLAN 10 traffic is allowed on the trunk.
3. Left trunk ready for future VLAN additions.

### Step 6 — Configure Device Network Settings

1. Connected all devices to VLAN 10 access ports (`Fa0/2–Fa0/5`).
2. Assigned each device a unique static IP in `192.168.88.x`.
3. Set each device's default gateway to `192.168.88.1`.

### Step 7 — Validate Connectivity

Verified all lab devices can:

- Ping each other
- Ping Cisco switch management IP `192.168.88.2`
- Ping MikroTik router `192.168.88.1` (when router VLAN config is correct)
- Reach each Proxmox web UI

### Step 8 — Confirm Security Baseline

1. Confirmed MikroTik router is on default firewall config.
2. Verified inbound internet traffic is blocked by default.
3. Confirmed no port forwarding/public exposure is active.
4. Confirmed no extra ACLs/firewall policy on Cisco Layer 2 switch (expected state).

---

## Current Network State

| Device | Switch Port | VLAN | IP Address | Gateway | Status |
|--------|-------------|------|------------|---------|--------|
| Management PC | Fa0/2 | 10 | 192.168.88.xx | 192.168.88.1 | Online |
| Proxmox Node 1 | Fa0/3 | 10 | 192.168.88.xx | 192.168.88.1 | Online |
| Proxmox Node 2 | Fa0/4 | 10 | 192.168.88.xx | 192.168.88.1 | Online |
| Proxmox Node 3 | Fa0/5 | 10 | 192.168.88.xx | 192.168.88.1 | Online |
| Cisco Switch (mgmt) | VLAN 10 SVI | 10 | 192.168.88.2 | 192.168.88.1 | Online |
| MikroTik Router | Fa0/1 (trunk) | ALL* | 192.168.88.1 (VLAN 10) | - | Online |

\* Trunk allows VLAN 10 and future VLANs as needed.

---

## Verification Checklist

- [ ] VLAN 10 (`LABS`) exists on switch
- [ ] `Fa0/2–Fa0/5` are assigned to VLAN 10 as access ports
- [ ] `Fa0/1` is trunked to MikroTik router
- [ ] Cisco switch management SVI is `192.168.88.2/24`
- [ ] Switch default gateway is `192.168.88.1`
- [ ] Management PC and all three Proxmox nodes can ping each other
- [ ] Devices can ping `192.168.88.2` and `192.168.88.1`
- [ ] Proxmox web UI is reachable for all nodes
- [ ] No unintended internet exposure is active

---

## Notes & Next Steps

- All major devices are now reachable in VLAN 10.
- One Proxmox node had connectivity issues and was corrected.
- Future work:
  - Verify/adjust MikroTik VLAN + firewall config if router reachability regresses
  - Add additional VLANs and subnet segmentation as the lab grows
  - Harden firewall rules as external exposure requirements change

---

## What's Next

**Phase 3: Proxmox Cluster Formation and Management Network Expansion**

Phase 3 will focus on clustering the Proxmox nodes, confirming stable inter-node communication, and preparing additional VLAN/subnet structure for separated management, storage, and workload traffic.
