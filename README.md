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
