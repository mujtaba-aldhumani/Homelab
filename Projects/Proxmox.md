## Goal

Build a virtualization host for learning IT infrastructure — the platform everything else in this vault runs on.

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

## Installation

Pre-installation checklist, completed before wiping the machine's existing Windows 11 Pro OEM install:

- Linked the Windows OEM digital license to a Microsoft account, and separately saved the OEM product key as a manual fallback (`(Get-CimInstance -ClassName SoftwareLicensingService).OA3xOriginalProductKey` — `wmic` is deprecated on current Windows builds)
- Confirmed BIOS virtualization settings: VT-x enabled, VT-d enabled, Secure Boot disabled, UEFI-only boot
- Identified the home subnet (`192.168.86.0/24`, gateway `192.168.86.1`) and verified `192.168.86.200` was unused via `arp -a` before assigning it as a static IP — see [Static IP over DHCP Reservation](../Decisions/Static%20IP%20over%20DHCP%20Reservation.md)
- Chose ext4 with LVM over ZFS for the single-disk system — see [Filesystem - ext4+LVM over ZFS](../Decisions/Filesystem%20-%20ext4+LVM%20over%20ZFS.md)

Install:

- Verified the Proxmox VE 9.2-1 ISO's SHA256 checksum before writing it to a USB installer
- Hit a "no device with valid iso found" boot error, then a post-install web UI/ping unreachable issue — both resolved; full diagnosis in [Proxmox Installation - USB Boot and Network Connectivity Issues](../Troubleshooting/Proxmox%20Installation%20-%20USB%20Boot%20and%20Network%20Connectivity%20Issues.md)
- Installed Proxmox VE 9.2-1: ext4 filesystem, `/dev/nvme0n1`, hostname `proxmox.lan`, IP `192.168.86.200/24`, gateway `192.168.86.1`, DNS `8.8.8.8` at install time (later pointed network-wide at Pi-hole, see [Pi-hole](Pi-hole.md))
- Confirmed web UI access at `https://192.168.86.200:8006`, ran initial package updates, switched from the enterprise repo to no-subscription

## Virtual Machines and LXC Containers

The host currently runs three VMs and one LXC container. Full build detail for each lives in its own dedicated project file:

| ID | Name | Type | Purpose | Project |
|---|---|---|---|---|
| 100 | windows11 | VM | General Windows practice VM, now the Active Directory client | [Windows 11 VM](Windows%2011%20VM.md) |
| 101 | tailscaleproxy | VM | Remote access (subnet router + exit node) | [Tailscale](Tailscale.md) |
| 102 | pihole | LXC | Network-wide DNS ad blocking | [Pi-hole](Pi-hole.md) |
| 103 | DC01 | VM | Active Directory domain controller | [Active Directory](Active%20Directory.md) |

## Status

Proxmox installed, updated, and running on the no-subscription repo. Four workloads deployed across three VMs and one LXC container — see the individual project files above for each one's current state.

## Related Decisions

- [Filesystem - ext4+LVM over ZFS](../Decisions/Filesystem%20-%20ext4+LVM%20over%20ZFS.md)
- [Static IP over DHCP Reservation](../Decisions/Static%20IP%20over%20DHCP%20Reservation.md)

## Project Log

### 2026-07-11

- Completed the pre-installation checklist, resolved the USB boot/connectivity issue, and installed Proxmox VE 9.2-1 — see [Daily Log — 2026-07-11](../Daily%20Logs/2026-07-11.md)

## Next Steps

1. Decide the next homelab project from the candidate rotation — see [Documentation & Planning](Documentation%20&%20Planning.md)
