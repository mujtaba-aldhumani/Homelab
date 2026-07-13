Filesystem - ext4+LVM over ZFS

## Decision

Used ext4 with LVM for the Proxmox host's storage, instead of ZFS.

## Context

Proxmox's installer offers a choice of filesystem for the boot/storage disk. The M720q has a single 500GB NVMe SSD, no additional drives.

## Reasoning

ZFS's headline benefits — redundancy against drive failure, self-healing via checksums — require at least two disks to actually provide protection. With only one disk, those benefits don't apply yet. Meanwhile ZFS carries real costs on a single-disk system: higher RAM usage for its caching layer (competes with RAM needed for VMs), and more total write amplification from copy-on-write, which wears a consumer-grade SSD faster than ext4 would.

## Alternatives Considered

ZFS was considered specifically for its snapshot and self-healing features, but the tradeoffs weren't worth it without a second disk to make redundancy meaningful.

## Future Note

If a second SSD is ever added specifically for VM storage, that would be a reasonable point to introduce ZFS with actual redundancy behind it.
