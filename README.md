# wireguard-vpn-lab
## Overview
This project demonstrates the setup and configuration of a secure WireGuard VPN server running on a Raspberry Pi.

The goal was to provide secure remote access to a home network while handling dynamic public IP changes using DuckDNS.

## Technologies Used
- WireGuard VPN
- Raspberry Pi (Kali Linux)
- DuckDNS (Dynamic DNS)
- Linux CLI
- QR Code provisioning for clients

## Network Setup
- VPN Network: 10.8.0.0/24
- Server: Raspberry Pi (home network)
- Clients: Mobile devices (iOS/Android)

## Key Features
- Full tunnel VPN configuration
- Dynamic DNS integration (DuckDNS)
- Secure key-based authentication
- Remote SSH access via VPN
- Client configuration automation

## Problem Solved
Initially, VPN access broke whenever the public IP changed.

Solution:
- Implemented DuckDNS
- Updated WireGuard endpoint to domain name instead of static IP
- Automated updates to maintain connectivity

## Commands Used

```bash
wg genkey
wg pubkey
wg-quick up wg0
wg show
```

## Network Architecture

```
Client (Phone)
      |
      |  WireGuard VPN (UDP 51820)
      |
[ Internet ]
      |
[ Home Router ]
      |
[ Raspberry Pi - WireGuard Server ]
      |
[ Local Network 192.168.1.0/24 ]
      |
[ Internal Devices (VMs, SSH, etc) ]
```

## Sample Client Configuration

ini
[Interface]
PrivateKey = (hidden)
Address = 10.8.0.11/32
DNS = 1.1.1.1

[Peer]
PublicKey = (server public key)
Endpoint = techmann.duckdns.org:51820
AllowedIPs = 0.0.0.0/0, ::/0
PersistentKeepalive = 25

## Troubleshooting

### Issue: VPN connects but cannot access LAN
- **Cause:** Missing route to local network
- **Fix:** Added local network to AllowedIPs
AllowedIPs = 0.0.0.0/0, ::/0, 192.168.1.0/24
---

### Issue: VPN stops working after public IP change
- **Cause:** Dynamic public IP from ISP
- **Fix:** Implemented DuckDNS and updated endpoint
Endpoint = techmann.duckdns.org:51820
---

### Issue: VPN handshake successful but no traffic (restricted WiFi)
- **Cause:** Network blocking or limiting UDP traffic
- **Observation:** VPN shows connected but no access to LAN or internet
- **Workarounds:**
- Switch to mobile data to establish connection
- Use alternative ports (e.g., 443)
- Consider UDP tunneling tools (advanced)

---

### Issue: Cannot SSH into home network via VPN
- **Cause:** SSH not allowed through firewall or incorrect routing
- **Fix:**
- Ensure SSH port (22) is open internally
- Confirm VPN subnet routing is correct
- Verify WireGuard interface is active
wg show
---

### Issue: Client not receiving IP / no connectivity
- **Cause:** Misconfigured client config or missing peer setup
- **Fix:**
- Verify client IP assignment (e.g., 10.8.0.x/32)
- Ensure peer is added on server
- Restart interface
wg-quick down wg0
wg-quick up wg0
---

### Issue: Config changes not applied
- **Cause:** WireGuard not reloaded
- **Fix:**
wg syncconf wg0 <(wg-quick strip wg0)
---

### Issue: DNS not resolving when connected to VPN
- **Cause:** Missing DNS configuration in client
- **Fix:**
DNS = 1.1.1.1
