# Phase 1 — Screenshots

Take the following screenshots **in order** and save them here with the filenames listed.

---

## Screenshot Checklist

### 1. `01-interfaces-list.png`
**Where:** Winbox → Interfaces (main Interfaces window)  
**What to show:**
- All interfaces listed (ether1 through ether5, bridge)
- The **Running** (green) indicator next to each active interface
- Interface names visible

---

### 2. `02-ip-addresses.png`
**Where:** Winbox → IP → Addresses  
**What to show:**
- `192.168.1.168/xx` assigned to `ether1` (from ISP modem)
- `192.168.88.1/24` assigned to the bridge interface
- Both entries visible in the same window

---

### 3. `03-dhcp-server.png`
**Where:** Winbox → IP → DHCP Server  
**What to show:**
- The DHCP server entry (name, interface = bridge, active status)
- Switch to the **Leases** tab for a second screenshot if you want to show assigned IPs

---

### 4. `04-dhcp-leases.png` *(optional but recommended)*
**Where:** Winbox → IP → DHCP Server → Leases tab  
**What to show:**
- Active leases with hostnames and assigned `192.168.88.x` IPs for each Proxmox node and wAP

---

### 5. `05-proxmox-ip-config.png`
**Where:** Terminal / shell on one of the Proxmox nodes  
**What to show:**
- Output of `ip addr show` confirming the node has a `192.168.88.x` address
- Hostname visible in the prompt or run `hostname` first

---

### 6. `06-ping-test.png` *(optional but recommended)*
**Where:** Terminal on a Proxmox node  
**What to show:**
- Output of `ping -c 4 192.168.88.1` (gateway reachable)
- Output of `ping -c 4 8.8.8.8` (internet reachable)

---

## Tips

- Use **full-screen** Winbox screenshots so labels are readable.
- If a window is too small, drag the column headers wider so IP addresses aren't truncated.
- Name files exactly as listed above so they match references in `README.md`.
