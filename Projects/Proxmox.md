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

## Planned Virtual Machines

### Ubuntu Server

Purpose:

- Linux administration
- SSH
- Docker
- Tailscale

### Windows Server

Purpose:

- Active Directory
- DNS
- Group Policy

## Status

Planning

## Notes

Waiting for installation.

## Project Log

### 2026-06-04

- Purchased Lenovo ThinkCentre M720q
- Installed Obsidian
- Created documentation vault
- Connected vault to GitHub
- Configured Obsidian Git
- Verified synchronization between M720q and ASUS laptop

## Next Steps

1. Download Proxmox ISO
2. Create bootable USB
3. Install Proxmox
4. Configure networking
5. Create Ubuntu Server VM

### 2026-07-11

- Completed pre-installation checklist: BIOS virtualization settings (VT-x, VT-d), Secure Boot disabled, UEFI-only confirmed
- Linked Windows OEM digital license to Microsoft account and saved product key as backup
- Planned network configuration: static IP 192.168.86.200/24, verified unused via arp -a
- Decided on ext4+LVM over ZFS (single-disk system, ZFS redundancy benefits require multiple disks)
- Verified Proxmox VE 9.2-1 ISO checksum before installation
- Resolved USB boot failure and post-install connectivity issue (see [[Proxmox Installation - USB Boot and Network Connectivity Issues]])
- Successfully installed Proxmox VE 9.2-1
- Confirmed web UI access at [https://192.168.86.200:8006](https://192.168.86.200:8006)
- Updated packages, switched to no-subscription repo

## Status

Updated from "Planning" to "Installed — awaiting first VM"

## Next Steps

1. Create first VM (Ubuntu Server or Windows)
2. Download Windows ISO + VirtIO driver ISO into Proxmox storage
3. Configure basic backup
4. Continue toward planned roadmap: Ubuntu Server → Tailscale → Docker → Active Directory → Monitoring → Wazuh