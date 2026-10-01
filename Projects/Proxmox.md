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

## Virtual Networks

| Bridge | Purpose | Physical port | Notes |
|---|---|---|---|
| `vmbr0` | Main lab network — all five original guests plus the host (`192.168.86.200/24`, gateway `192.168.86.1`) | `nic0` | Not VLAN aware |
| `vmbr1` | Isolated sandbox for the [OPNsense Segmentation Lab](OPNsense%20Segmentation%20Lab.md) | none | VLAN aware, no host IP address |

The host also lists a second wired interface (`nic1`) and a wireless one (`wlp2s0`), both present but unconfigured.

## Virtual Machines and LXC Containers

The host currently runs four VMs and two LXC containers (plus temporary test LXCs for the segmentation lab: `lantest`, `server-test`, `client-test`). Full build detail for each lives in its own dedicated project file:

| ID | Name | Type | Purpose | Project |
|---|---|---|---|---|
| 100 | windows11 | VM | General Windows practice VM, now the Active Directory client | [Windows 11 VM](Windows%2011%20VM.md) |
| 101 | tailscaleproxy | VM | Remote access (subnet router + exit node) | [Tailscale](Tailscale.md) |
| 102 | pihole | LXC | Network-wide DNS ad blocking | [Pi-hole](Pi-hole.md) |
| 103 | DC01 | VM | Active Directory domain controller | [Active Directory](Active%20Directory.md) |
| 104 | zammad | LXC | Helpdesk/ticketing system | [Zammad](Zammad.md) |
| 105 | opnsense | VM | Firewall/router for the isolated segmentation sandbox | [OPNsense Segmentation Lab](OPNsense%20Segmentation%20Lab.md) |

## Status

Proxmox installed, updated, and running on the no-subscription repo. Six workloads deployed across four VMs and two LXC containers (plus temporary test LXCs for the OPNsense Segmentation Lab: `lantest`, `server-test`, `client-test`) — see the individual project files above for each one's current state.

## Related Decisions

- [Filesystem - ext4+LVM over ZFS](../Decisions/Filesystem%20-%20ext4+LVM%20over%20ZFS.md)
- [Static IP over DHCP Reservation](../Decisions/Static%20IP%20over%20DHCP%20Reservation.md)

## Project Log

### 2026-07-11

- Completed the pre-installation checklist, resolved the USB boot/connectivity issue, and installed Proxmox VE 9.2-1 — see [Daily Log — 2026-07-11](../Daily%20Logs/2026-07-11.md)

### 2026-07-12

- Built the Windows 11 practice VM (VMID 100) and the Ubuntu Server VM (VMID 101) — see [Windows 11 VM](Windows%2011%20VM.md) and [Tailscale](Tailscale.md)

### 2026-07-14

- Built the Pi-hole LXC (VMID 102) — see [Pi-hole](Pi-hole.md)

### 2026-08-27

- Built the Windows Server domain controller VM (VMID 103) — see [Active Directory](Active%20Directory.md)

### 2026-09-14

- Built the Zammad LXC (VMID 104) — see [Zammad](Zammad.md)

### 2026-09-28

- Created the isolated `vmbr1` bridge for the [OPNsense Segmentation Lab](OPNsense%20Segmentation%20Lab.md)

### 2026-09-29

- Built and installed the OPNsense VM (VMID 105) — see [OPNsense Segmentation Lab](OPNsense%20Segmentation%20Lab.md)

### 2026-09-30

- Created both VLANs on the sandbox, fixed DHCP, proved default-deny, and tested a working inter-VLAN rule — see [OPNsense Segmentation Lab](OPNsense%20Segmentation%20Lab.md)

## Next Steps

1. Decide the next project in the rotation (Wazuh SIEM is the leading candidate) — see [Documentation & Planning](Documentation%20&%20Planning.md)
