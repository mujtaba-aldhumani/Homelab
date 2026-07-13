Static IP over DHCP Reservation

## Decision

Configured the Proxmox host (and later the Ubuntu Tailscale VM) with a manually-set static IP, rather than a DHCP reservation through the router.

## Context

Both approaches would achieve the same practical result — an address that never changes for these devices, so they stay reachable at a consistent URL/IP over time, including after moving the physical hardware to a different Google Wifi mesh node.

## Reasoning

No admin access to the Google Wifi app, which is where a DHCP reservation would normally be configured. A manually-set static IP achieves the same outcome without needing that access.

## Details

- Subnet identified via `ipconfig`: 192.168.86.0/24, gateway 192.168.86.1
- Confirmed candidate addresses were unused via `arp -a` before assigning
- Proxmox host: 192.168.86.200
- Ubuntu Tailscale VM: 192.168.86.201
