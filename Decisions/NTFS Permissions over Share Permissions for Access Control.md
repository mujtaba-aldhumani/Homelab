NTFS Permissions over Share Permissions for Access Control

## Decision

Set share permissions loosely (`Authenticated Users: Full Control`) on the `CompanyShare` folder, and did all real access control at the NTFS layer instead — granting department security groups (`IT-Staff`, `Sales-Staff`, `HR-Staff`) Modify access on their own subfolder only, rather than splitting restrictions across both permission systems.

## Context

Any network folder is governed by two separate, stacking permission systems: share permissions (access over the network) and NTFS permissions (access on disk, regardless of path taken). When both apply to the same folder, the more restrictive one wins.

## Reasoning

Maintaining real logic in both layers means two separate places a misconfiguration can silently break access, and two places to check when troubleshooting a permissions issue. Standard real-world practice collapses this into one layer — set the share permission loose enough that it's never the bottleneck, and manage every actual restriction through NTFS, since NTFS permissions are more granular and are what's actually being audited/reviewed in most environments.

## Details

- Folder structure: `C:\CompanyShare\` with `IT`, `Sales`, `HR`, and `Public` subfolders
- Share-level: `Authenticated Users: Full Control`
- NTFS-level per department folder: `SYSTEM` and `Administrators` at Full Control (required for OS operations and ongoing manageability), plus the matching department group at Modify, with all other inherited entries removed
- `CREATOR OWNER` deliberately excluded from department folders — it only grants the original creator of a specific file extra rights beyond their group's baseline, which wasn't the scenario here since the department group's Modify access already covers every member equally
- `Public` folder: `Domain Users: Read & Execute` only, no write access
- Verified via cross-account testing: `alex.chen` (IT) could access/write to `IT`, was denied on `Sales`, and could read but not write to `Public`; same pattern confirmed in reverse for `jordan.lee` (Sales)

## Status

Adopted.

## Related

- [Active Directory](../Projects/Active%20Directory.md)
