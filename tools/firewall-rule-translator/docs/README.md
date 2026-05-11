# Firewall Rule Translator

Convert firewall rules between iptables, UFW, firewalld, Windows Firewall, and cloud providers.

## Features

- Configure firewall rules with visual interface
- Translate to multiple formats:
  - iptables (Linux)
  - UFW (Uncomplicated Firewall)
  - firewalld (Linux)
  - Windows Firewall
  - AWS Security Groups
  - Azure NSG
  - GCP Firewall Rules
- Support for TCP, UDP, ICMP protocols
- Port ranges and CIDR notation
- Inbound/outbound directions
- Allow/deny actions
- Optional descriptions

## Usage

1. Configure rule parameters (action, direction, protocol, ports, IPs)
2. Add optional description
3. Click "Translate to All Formats"
4. View rules in all supported formats
5. Copy specific format needed

## Requirements

- Modern web browser
- No server or dependencies required
- Works completely offline
