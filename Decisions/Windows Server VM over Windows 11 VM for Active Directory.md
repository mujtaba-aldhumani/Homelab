Windows Server VM over Windows 11 VM for Active Directory

## Decision

Built a new VM (DC01, VMID 103) running Windows Server 2022 Standard Evaluation as the Active Directory domain controller, rather than installing AD DS on the existing Windows 11 practice VM (VMID 100).

## Context

The AD project was originally planned to run on the existing Windows 11 VM, since it was already built and sitting idle.

## Reasoning

Windows 11 (and Windows client editions generally) cannot run Active Directory Domain Services at all — it's a Windows Server-only role, not a hidden or licensed-off feature on client SKUs. There was no version of "install AD DS on VM100" that could have worked.

## Details

- New VM built: Server 2022 Standard Evaluation (Desktop Experience), q35 machine, OVMF/UEFI with Secure Boot, TPM v2.0, VirtIO SCSI disk, 4096MB RAM, 2 cores
- VirtIO driver ISO required during install for the storage controller to be visible to Windows Setup
- VM100 (Windows 11) repurposed as the plan's future domain-joined client instead of a DC

## Status

Adopted.

## Related

- [Proxmox](../Projects/Proxmox.md)
