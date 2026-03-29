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

## ### 🛠 Troubleshooting Guide

| Issue | Potential Cause | Recommended Fix |
| :--- | :--- | :--- |
| **Connected, no LAN access** | Missing static route to local subnet. | Add local network (e.g., `192.168.1.0/24`) to `AllowedIPs`. |
| **Fails after Public IP change** | ISP rotated your dynamic IP. | Use DuckDNS; update `Endpoint` to your domain name. |
| **Handshake OK, no traffic** | UDP traffic being throttled/blocked. | Switch to mobile data or use an alternative port (e.g., `443`). |
| **Cannot SSH via VPN** | Firewall blocking Port 22 or bad routing. | Ensure Port 22 is open; verify routing with `wg show`. |
| **No IP / No connectivity** | Peer not added or misconfigured client. | Verify client IP (e.g., `10.8.0.x`); restart with `wg-quick`. |
| **Changes not applying** | WireGuard service wasn't refreshed. | Run `wg syncconf` or restart the interface. |
| **DNS not resolving** | Missing DNS entry in client config. | Add `DNS = 1.1.1.1` to the `[Interface]` section. |

---

## Security Hardening

### Key Security Measures Implemented

- **Private key protection**
  - Client and server private keys are never exposed or shared
  - Sensitive values removed from all shared configurations

- **Restricted SSH access**
  - SSH access limited to VPN network only
  - Public SSH exposure avoided where possible

- **Firewall considerations**
  - Only required port (UDP 51820) exposed on router
  - Internal services restricted to local/VPN network

- **PersistentKeepalive configuration**
  - Maintains secure and stable tunnel for remote clients behind NAT

---

### Recommended Improvements (Future Work)

- Implement firewall rules using pfSense
- Restrict SSH access strictly to VPN subnet (10.8.0.0/24)
- Disable password authentication for SSH (use key-based login only)
- Add intrusion detection (e.g., Fail2Ban)
- Use non-standard ports where appropriate to reduce scanning exposure

---

### Security Considerations

- VPN provides encryption but does not eliminate endpoint risks
- Device security (client-side) remains critical
- Strong key management is essential for maintaining secure access
