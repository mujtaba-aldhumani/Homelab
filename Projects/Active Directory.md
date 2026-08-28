## Goal

Stand up a working Active Directory domain from scratch — a domain controller, a realistic OU/security group structure, a real Group Policy, and a domain-joined client to prove enforcement — as a sysadmin-track portfolio project.

## Virtual Machines

### Windows Server / Domain Controller (DC01 / VMID 103)

Purpose: Active Directory domain controller for `mujtaba.internal` — DNS, Group Policy, user/group management. Built as a dedicated VM rather than repurposing the existing Windows 11 VM, since Windows 11 client edition cannot run AD DS at all — see [Windows Server VM over Windows 11 VM for Active Directory](../Decisions/Windows%20Server%20VM%20over%20Windows%2011%20VM%20for%20Active%20Directory.md).

Status: Installed, promoted to first domain controller of a new forest, running. `dcdiag` passing clean.

Specs: q35 machine, OVMF (UEFI) with Secure Boot, TPM v2.0, VirtIO SCSI disk (40GB), VirtIO NIC, 4096MB RAM, 2 cores. Static IP `192.168.86.203` (see [Static IP over DHCP Reservation](../Decisions/Static%20IP%20over%20DHCP%20Reservation.md)), self-hosted DNS (127.0.0.1 preferred, 8.8.8.8 alternate).

### Windows 11 (windows11 / VMID 100)

Domain-joined as the AD project's client machine as of 2026-08-27. Full VM build history in [Windows 11 VM](Windows%2011%20VM.md).

## Domain Setup

- Domain name: `mujtaba.internal`, chosen over `.local` or a public-domain subdomain — see [Reserved .internal TLD over .local for AD Domain Name](../Decisions/Reserved%20.internal%20TLD%20over%20.local%20for%20AD%20Domain%20Name.md)
- Promoted DC01 as the first DC of a new forest, NetBIOS name `MUJTABA`
- Post-promotion, repointed DC01's own DNS to itself (127.0.0.1 preferred, 8.8.8.8 alternate as fallback only)
- Set an external NTP source (`time.windows.com`, `pool.ntp.org`) and corrected the timezone to EST — DC01 is the forest's PDC emulator with nothing above it in the time hierarchy by default, so it needed a manual external time source
- `dcdiag` run after the NTP fix — all tests passing clean (initial run showed transient DFSREvent/SystemLog warnings tied to the fresh promotion, which cleared)

## OU and Group Structure

Built a small, lean fictional-company structure rather than deep realism, for portfolio purposes:

- Top-level OUs: `_Admin`, `Groups`, `Service Accounts`
- `Departments` parent OU containing `Sales`, `IT`, `HR` sub-OUs
- Personal admin account created in `_Admin`, added to Domain Admins — kept separate from a future regular daily-use account, per least-privilege practice (never use the built-in Administrator account for daily work)
- One fictional user per department, each with Department/Job Title attributes filled in: Sam Patel (HR), Alex Chen (IT), Jordan Lee (Sales)
- Matching security groups in the `Groups` OU (Global scope): `IT-Staff`, `Sales-Staff`, `HR-Staff`, each with the corresponding department's user as its only member — kept structurally separate from the OU tree, since OUs (organization/policy scope) and security groups (access control) serve different purposes even when they mirror each other

## Group Policy

- Created GPO "IT - Restrict Control Panel" (User Configuration > Administrative Templates > Control Panel > *Prohibit access to Control Panel and PC settings*, Enabled)
- Linked once at the `Departments` parent OU rather than three separate per-department links, so it inherits down to `Sales`/`IT`/`HR` automatically
- Verified inheritance for Jordan Lee and Sam Patel via the Group Policy Modeling Wizard — `gpresult` couldn't be used for them, since domain users are blocked from logging into the DC itself, so no RSoP cache existed yet ([gpresult Shows No RSoP Data for Users Who Have Never Logged In](../Troubleshooting/gpresult%20Shows%20No%20RSoP%20Data%20for%20Users%20Who%20Have%20Never%20Logged%20In.md))
- Domain-joined VM100 (Windows 11) to `mujtaba.internal`, DNS pointed at DC01 first
- Logged into VM100 live as `alex.chen` and confirmed Control Panel access is actually blocked — full chain (OU → group → GPO → inheritance → client enforcement) verified end-to-end

## Troubleshooting

- Ctrl+Alt+Del didn't reach the Windows Server lock screen through the noVNC console — [Ctrl+Alt+Del Not Reaching Proxmox noVNC Console](../Troubleshooting/Ctrl+Alt+Del%20Not%20Reaching%20Proxmox%20noVNC%20Console.md)
- `alex.chen` couldn't log into DC01 directly to test the GPO ("sign-in method isn't allowed") — [Domain User Login Denied on Domain Controller](../Troubleshooting/Domain%20User%20Login%20Denied%20on%20Domain%20Controller.md)
- `gpresult` reported no RSoP data for `jordan.lee` and `sam.patel` — [gpresult Shows No RSoP Data for Users Who Have Never Logged In](../Troubleshooting/gpresult%20Shows%20No%20RSoP%20Data%20for%20Users%20Who%20Have%20Never%20Logged%20In.md)

## Status

Core build complete and verified end-to-end: DC promoted, OUs and groups built, one GPO created and confirmed inherited/enforced against a real domain-joined client. Fully functional single-domain AD forest with a realistic small-company structure.

## Related Decisions

- [Windows Server VM over Windows 11 VM for Active Directory](../Decisions/Windows%20Server%20VM%20over%20Windows%2011%20VM%20for%20Active%20Directory.md)
- [Reserved .internal TLD over .local for AD Domain Name](../Decisions/Reserved%20.internal%20TLD%20over%20.local%20for%20AD%20Domain%20Name.md)
- [Static IP over DHCP Reservation](../Decisions/Static%20IP%20over%20DHCP%20Reservation.md)

## Project Log

### 2026-08-27

- Built DC01 (VMID 103, Windows Server 2022) and promoted it to the first domain controller of a new forest, `mujtaba.internal`
- Set external NTP source and corrected timezone; confirmed `dcdiag` passing clean
- Built OU structure (`_Admin`, `Groups`, `Service Accounts`, `Departments` > `Sales`/`IT`/`HR`) and populated it with a personal admin account, three department users, and three matching security groups
- Created GPO "IT - Restrict Control Panel," linked at the `Departments` parent OU
- Domain-joined VM100 and confirmed the policy enforced live for `alex.chen`
- Full walkthrough and troubleshooting: [Daily Log — 2026-08-27](../Daily%20Logs/2026-08-27.md)

## Next Steps

1. Decide next AD sub-task: shared folder with group-based NTFS permissions, additional GPOs (mapped drives, wallpaper), or move to a different homelab project from the candidate rotation
