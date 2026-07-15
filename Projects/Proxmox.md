## Goal

Build a virtualization environment for learning IT infrastructure.

## Hardware

- Lenovo ThinkCentre M720q
- CPU: Intel i5-8400T
- RAM: 16GB
- Storage: 500GB SSD

## Planned Skills

- Virtualization
- Linux Administration
- Docker
- Networking
- Active Directory
- Security Monitoring
- Remote Access

## Virtual Machines

### Windows 11 (windows11 / VMID 100)

Status: Installed, unactivated (see [Windows Practice VM - Left Unactivated](../Decisions/Windows%20Practice%20VM%20-%20Left%20Unactivated.md)), currently shut down. Practice/learning VM, not daily-use.

Specs: q35 machine, OVMF (UEFI), TPM v2.0, VirtIO SCSI disk (64GB), VirtIO NIC, 4096MB RAM, 2 cores.

![Windows 11 VM hardware config](../Screenshots/windows11-vm-config.png)
*Final hardware config before first boot.*

### Ubuntu Server / Tailscale Proxy (tailscaleproxy / VMID 101)

Purpose: general remote access to home infrastructure (subnet router), and an exit node so specific devices (brother's Google TV, personal iPhone) can share the home IP for a Stremio + Real-Debrid setup (see [Tailscale Exit Node over SOCKS5 Proxy for Stremio-RD](../Decisions/Tailscale%20Exit%20Node%20over%20SOCKS5%20Proxy%20for%20Stremio-RD.md), supersedes [Tailscale Proxy Approach for Stremio-RD](../Decisions/Tailscale%20Proxy%20Approach%20for%20Stremio-RD.md)).

Status: Installed, running. Static IP 192.168.86.201 (see [Static IP over DHCP Reservation](../Decisions/Static%20IP%20over%20DHCP%20Reservation.md)), Tailscale IP 100.74.175.81, hostname `tailscaleproxy`. Advertised and approved as both a subnet router (192.168.86.0/24) and an exit node. Dante SOCKS5 proxy also installed and confirmed working (port 1080), though not the path currently used for Stremio sharing.

Specs: SeaBIOS, i440fx machine, VirtIO SCSI disk (20GB), VirtIO NIC, 2048MB RAM, 1 core.

![Tailscale devices connected](../Screenshots/tailscale-both-devices-connected.png)
*Ubuntu VM and Windows machine both joined to the same tailnet, connectivity confirmed via ping.*

### Windows Server (planned, not yet built)

Purpose:

- Active Directory
- DNS
- Group Policy

## LXC Containers

### Pi-hole (pihole / VMID 102)

Purpose: network-wide DNS-based ad blocking for the home network. Built via Proxmox Community Scripts rather than manual `pct create` (see [Proxmox Community Scripts over Manual LXC Creation for Pi-hole](../Decisions/Proxmox%20Community%20Scripts%20over%20Manual%20LXC%20Creation%20for%20Pi-hole.md)).

Status: Installed, running, verified end-to-end (`pihole status`, admin dashboard reachable and logged into). Blocklist populated automatically during install (StevenBlack/hosts, ~76,000 domains). Not yet handling network-wide traffic — DHCP/DNS on the home router (Google Wifi) still needs to be pointed at it; blocked on the brother's Google account access, see Status below. Declined the installer's optional Unbound add-on for now (see [Unbound Deferred as a Separate Follow-Up Project](../Decisions/Unbound%20Deferred%20as%20a%20Separate%20Follow-Up%20Project.md)).

Specs: Unprivileged LXC, Debian 13, 1 core, 512MB RAM, 2GB disk, static IP 192.168.86.202/24 (see [Static IP over DHCP Reservation](../Decisions/Static%20IP%20over%20DHCP%20Reservation.md)), gateway 192.168.86.1, IPv6 disabled, FUSE and TUN/TAP both disabled.

Full build/troubleshooting detail: [Daily Log — 2026-07-14](../Daily%20Logs/2026-07-14.md).

## Status

Proxmox installed and running. Two VMs built (Windows 11 practice VM, Ubuntu Tailscale proxy VM), both now joined to the tailnet. Tailscale subnet routing (whole home LAN reachable remotely) and exit node (for sharing the home IP with specific outside devices) both configured and confirmed working on `tailscaleproxy`.

## Related Decisions

- [Filesystem - ext4+LVM over ZFS](../Decisions/Filesystem%20-%20ext4+LVM%20over%20ZFS.md)
- [Static IP over DHCP Reservation](../Decisions/Static%20IP%20over%20DHCP%20Reservation.md)
- [Claude Desktop - Filesystem MCP over Code Tab](../Decisions/Claude%20Desktop%20-%20Filesystem%20MCP%20over%20Code%20Tab.md)
- [Windows Practice VM - Left Unactivated](../Decisions/Windows%20Practice%20VM%20-%20Left%20Unactivated.md)
- [Tailscale Proxy Approach for Stremio-RD](../Decisions/Tailscale%20Proxy%20Approach%20for%20Stremio-RD.md) (superseded)
- [Tailscale Exit Node over SOCKS5 Proxy for Stremio-RD](../Decisions/Tailscale%20Exit%20Node%20over%20SOCKS5%20Proxy%20for%20Stremio-RD.md)
- [SSH over Proxmox Console for Linux VM Management](../Decisions/SSH%20over%20Proxmox%20Console%20for%20Linux%20VM%20Management.md)
- [Vault Separation - Homelab vs Life](../Decisions/Vault%20Separation%20-%20Homelab%20vs%20Life.md)
- [Proxmox Community Scripts over Manual LXC Creation for Pi-hole](../Decisions/Proxmox%20Community%20Scripts%20over%20Manual%20LXC%20Creation%20for%20Pi-hole.md)
- [Unbound Deferred as a Separate Follow-Up Project](../Decisions/Unbound%20Deferred%20as%20a%20Separate%20Follow-Up%20Project.md)

## Project Log

### 2026-06-04

- Purchased Lenovo ThinkCentre M720q
- Installed Obsidian
- Created documentation vault
- Connected vault to GitHub
- Configured Obsidian Git
- Verified synchronization between M720q and ASUS laptop

### 2026-07-11

- Completed pre-installation checklist: BIOS virtualization settings (VT-x, VT-d), Secure Boot disabled, UEFI-only confirmed
- Linked Windows OEM digital license to Microsoft account and saved product key as backup
- Planned network configuration: static IP 192.168.86.200/24, verified unused via arp -a (see [Static IP over DHCP Reservation](../Decisions/Static%20IP%20over%20DHCP%20Reservation.md))
- Decided on ext4+LVM over ZFS (see [Filesystem - ext4+LVM over ZFS](../Decisions/Filesystem%20-%20ext4+LVM%20over%20ZFS.md))
- Verified Proxmox VE 9.2-1 ISO checksum before installation
- Resolved USB boot failure and post-install connectivity issue (see [Proxmox Installation - USB Boot and Network Connectivity Issues](../Troubleshooting/Proxmox%20Installation%20-%20USB%20Boot%20and%20Network%20Connectivity%20Issues.md))
- Successfully installed Proxmox VE 9.2-1
- Confirmed web UI access at [https://192.168.86.200:8006](https://192.168.86.200:8006)
- Updated packages, switched to no-subscription repo

### 2026-07-12

- Set up Claude Desktop filesystem MCP integration to read/write directly into this vault (see [Claude Desktop - Filesystem MCP over Code Tab](../Decisions/Claude%20Desktop%20-%20Filesystem%20MCP%20over%20Code%20Tab.md))
- Built Windows 11 practice VM (VMID 100) — see [2026-07-12](../Daily%20Logs/2026-07-12.md) for full detail on driver loading, activation issue, and backup restore; activation decision in [Windows Practice VM - Left Unactivated](../Decisions/Windows%20Practice%20VM%20-%20Left%20Unactivated.md)
- Built Ubuntu Server VM (VMID 101) for Tailscale — static IP 192.168.86.201
- Installed and authenticated Tailscale on the Ubuntu VM and on the personal laptop, joining both to the same tailnet
- Confirmed working connectivity between both devices over Tailscale (ping test successful)
- Clarified requirement for the Stremio/RD use case — see [Tailscale Proxy Approach for Stremio-RD](../Decisions/Tailscale%20Proxy%20Approach%20for%20Stremio-RD.md)

### 2026-07-13

- Corrected 2026-07-12 log: the Windows Tailscale device was the personal laptop, not the Windows 11 practice VM; joined the Windows 11 VM to the tailnet separately (100.76.5.113)
- Set up Tailscale subnet routing so the whole home LAN (192.168.86.0/24) is reachable remotely, not just individually-joined devices — see [2026-07-13](../Daily%20Logs/2026-07-13.md) for full walkthrough
- Installed and configured Dante SOCKS5 proxy on `tailscaleproxy`, confirmed working via external curl test — later superseded for the Stremio/RD use case, see [Tailscale Exit Node over SOCKS5 Proxy for Stremio-RD](../Decisions/Tailscale%20Exit%20Node%20over%20SOCKS5%20Proxy%20for%20Stremio-RD.md)
- Discovered Stremio has no native proxy support; re-scoped the Stremio/RD sharing requirement to two specific devices and switched to Tailscale's exit-node feature instead
- Advertised and approved `tailscaleproxy` as a Tailscale exit node
- Ran into and resolved several troubleshooting issues along the way — see [IP Forwarding Not Persisting After Reboot](../Troubleshooting/IP%20Forwarding%20Not%20Persisting%20After%20Reboot.md) and [Stremio Has No Native Proxy Support](../Troubleshooting/Stremio%20Has%20No%20Native%20Proxy%20Support.md)

### 2026-07-14

- Built Pi-hole LXC (VMID 102) for network-wide ad blocking, via Proxmox Community Scripts (see [Proxmox Community Scripts over Manual LXC Creation for Pi-hole](../Decisions/Proxmox%20Community%20Scripts%20over%20Manual%20LXC%20Creation%20for%20Pi-hole.md))
- Hit and resolved several unrelated tooling issues along the way — see [Daily Log — 2026-07-14](../Daily%20Logs/2026-07-14.md) for the full walkthrough, or individually: [Bracketed-Paste Corruption in Proxmox noVNC Console](../Troubleshooting/Bracketed-Paste%20Corruption%20in%20Proxmox%20noVNC%20Console.md), [QuickEdit Mode and noVNC Selection Freezing Console Input](../Troubleshooting/QuickEdit%20Mode%20and%20noVNC%20Selection%20Freezing%20Console%20Input.md), [raw.githubusercontent.com Intermittent DNS Resolution Failures](../Troubleshooting/raw.githubusercontent.com%20Intermittent%20DNS%20Resolution%20Failures.md), [Piped Install Script Swallowing Interactive Confirmation Prompt](../Troubleshooting/Piped%20Install%20Script%20Swallowing%20Interactive%20Confirmation%20Prompt.md), [pct exec Not Resolving Binary via PATH](../Troubleshooting/pct%20exec%20Not%20Resolving%20Binary%20via%20PATH.md)
- Confirmed Pi-hole installed, running, and reachable at `http://192.168.86.202/admin`; admin password set
- Declined the installer's optional Unbound add-on for now (see [Unbound Deferred as a Separate Follow-Up Project](../Decisions/Unbound%20Deferred%20as%20a%20Separate%20Follow-Up%20Project.md))
- Network-wide DNS switch blocked on brother's Google Wifi/Google Home account access (network is under his account; he's currently out of town, but this can be granted remotely — doesn't require him to be home)

## Next Steps

1. Install Tailscale on the brother's Google TV and personal iPhone, and use `tailscaleproxy` as their exit node
2. Revisit Dante/a debrid-proxy addon later if a broader (non-two-person) shared-proxy setup is wanted
3. Get added as a Home Member (Admin) on the Google Wifi network, then set its custom DNS to `192.168.86.202` to make Pi-hole network-wide
4. Revisit Unbound for Pi-hole once the base DNS setup is proven stable (see [Unbound Deferred as a Separate Follow-Up Project](../Decisions/Unbound%20Deferred%20as%20a%20Separate%20Follow-Up%20Project.md))
5. Continue toward planned roadmap: Docker → Active Directory → Monitoring → Wazuh
