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

## Installing Proxmox VE on 2018 Intel Mac Mini Nodes

The 2018 Intel Mac Mini requires extra firmware steps before Proxmox can boot from USB. Apple's Startup Security Utility blocks unsigned operating systems by default.

### Step 1 — Enter Recovery Mode

1. Shut the Mac Mini down completely.
2. Hold **⌘ + R** immediately after pressing the power button and keep holding until you see the Apple logo or spinning globe.
   - If the Mac is connected to a Windows keyboard, use **Win + R** instead.
3. **If the timing is too tight**, boot normally, open Terminal, and run:
   ```bash
   sudo nvram "recovery-boot-mode=unused"
   sudo reboot
   ```
   The Mac will restart directly into Recovery Mode.

### Step 2 — Disable Startup Security

Once in Recovery Mode:

1. Open **Utilities → Startup Security Utility** from the menu bar.
2. Authenticate with your Mac admin password when prompted.
3. Under **Secure Boot**, select **No Security**.
4. Under **External Boot**, select **Allow booting from external or removable media**.
5. Close the utility and reboot.

> **Why this is needed:** Apple's firmware enforces a chain-of-trust that requires a signed bootloader. Proxmox VE uses an unsigned EFI binary, so Secure Boot must be disabled.

### Step 3 — Boot from Proxmox USB

1. Insert your Proxmox VE USB installer.
2. Power on the Mac Mini and hold **Option (⌥)** to open the boot picker.
3. Select the **EFI Boot** entry for the USB drive.

> **If the USB does not appear in the boot picker**, use the `bless` command to force it (see Step 3a below).

#### Step 3a — Force USB Boot with `bless` (if needed)

```bash
# 1. Find the USB disk identifier
diskutil list
# Look for your USB (e.g., /dev/disk2)

# 2. Mount the EFI partition
diskutil mount /dev/disk2s1

# 3. Set the Proxmox EFI binary as the boot target
sudo bless \
  --mount /Volumes/EFI \
  --setBoot \
  --file /Volumes/EFI/EFI/BOOT/BOOTX64.EFI \
  --shortform
```

| Flag | Purpose |
|------|---------|
| `--mount` | Points to the mounted EFI partition |
| `--setBoot` | Sets this entry as the next boot target in NVRAM |
| `--file` | Path to the EFI binary to boot |
| `--shortform` | Uses the shortened device path for wider firmware compatibility |

Reboot after running the command — the Mac will boot directly into the Proxmox installer.

### Step 4 — Install Proxmox VE

Work through the Proxmox VE installer:

1. **Target disk** — select the internal SSD (the Mac Mini's NVMe drive will appear here).
2. **Location and timezone** — set as appropriate.
3. **Password and email** — set a strong root password; the email is used for alerts.
4. **Network configuration** — assign a static IP on the `192.168.88.x` subnet:

   | Field | Example value |
   |-------|--------------|
   | Management interface | `enp0s31f6` (or whatever NIC appears) |
   | Hostname (FQDN) | `pve1.local` / `pve2.local` / `pve3.local` |
   | IP address | `192.168.88.10` / `.11` / `.12` |
   | Netmask | `255.255.255.0` |
   | Gateway | `192.168.88.1` |
   | DNS server | `192.168.88.1` |

5. Confirm and let the installer complete. The node will reboot.

### Step 5 — Access the Proxmox Web UI

After reboot, open a browser on any device connected to the `192.168.88.x` network:

```
https://<node-ip>:8006
```

For example: `https://192.168.88.10:8006`

- Accept the self-signed certificate warning.
- Log in as `root` with the password you set during installation.

> The `192.168.88.x` addresses are private (RFC 1918) and only reachable from inside your home network — they are safe to document here.

### Node Verification Checklist

- [ ] All three Mac Minis boot into Proxmox VE without errors
- [ ] Each node has its correct static IP (`192.168.88.10`, `.11`, `.12`)
- [ ] Each node can ping the gateway: `ping 192.168.88.1`
- [ ] Each node can ping the internet: `ping 8.8.8.8`
- [ ] Proxmox web UI is reachable at `https://<node-ip>:8006` for each node
- [ ] Root login works on all three nodes
- [ ] All three nodes appear under **Datacenter** in the Proxmox web UI (after clustering — Phase 3)

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
