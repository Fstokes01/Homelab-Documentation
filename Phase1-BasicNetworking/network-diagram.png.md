# network-diagram.png

This is a placeholder for the Phase 1 network topology diagram.

## What the Diagram Should Show

Create a diagram (using draw.io, Excalidraw, Lucidchart, or any tool you prefer) and export it as `network-diagram.png`. Save it in this folder alongside this file.

---

## Diagram Layout

```
┌─────────────────────────────────────────────────────────────┐
│                         INTERNET                            │
└───────────────────────────┬─────────────────────────────────┘
                            │
                ┌───────────▼───────────┐
                │      ISP Modem        │
                │   192.168.1.x (DHCP)  │
                └───────────┬───────────┘
                            │ WAN
                ┌───────────▼───────────┐
                │   MikroTik hEX Router │
                │  ether1: 192.168.1.168│  ← WAN (from modem)
                │  bridge: 192.168.88.1 │  ← LAN gateway
                └──┬────┬────┬────┬────┘
                   │    │    │    │
              eth2 │  eth3  eth4  eth5
                   │    │    │    │
           ┌───────▼┐ ┌─▼──┐ ┌▼──┐ ┌──▼──────┐
           │Proxmox │ │Prox│ │Prox│ │  WiFi   │
           │Node 1  │ │ N2 │ │ N3 │ │  AP     │
           │.88.x   │ │.88.x│ │.88.x│ │(wAP)   │
           └────────┘ └────┘ └────┘ └─────────┘
```

## Suggested Labels for the Diagram

- **ISP Modem:** `192.168.1.x` (DHCP pool from ISP)
- **hEX ether1 (WAN):** `192.168.1.168` (assigned by modem)
- **hEX LAN bridge:** `192.168.88.1/24`
- **Proxmox Node 1–3:** `192.168.88.x` (DHCP from hEX)
- **wAP:** `192.168.88.x` (DHCP from hEX)
- **Double NAT note:** Label the two NAT boundaries (modem → hEX, hEX → LAN)

## Recommended Tools

| Tool | URL | Notes |
|------|-----|-------|
| draw.io (diagrams.net) | https://app.diagrams.net | Free, exports PNG, no account needed |
| Excalidraw | https://excalidraw.com | Free, good for hand-drawn style diagrams |
| Lucidchart | https://lucidchart.com | Free tier available |
