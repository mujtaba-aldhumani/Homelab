Proxmox Community Scripts over Manual LXC Creation for Pi-hole

## Decision

Deploy Pi-hole using the Proxmox VE Helper-Scripts (community-scripts) automated LXC installer, rather than manually creating the container (`pct create`) and installing Pi-hole by hand.

## Context

Pi-hole needed a home: a lightweight LXC container rather than a full VM, since it doesn't need its own kernel and benefits from the fast boot / low overhead of a container for always-on DNS infrastructure. The question was whether to build that container manually (choosing the template, disk/CPU/RAM, network settings, and installing Pi-hole's own installer by hand) or use the community-maintained helper script that automates all of that into a single guided wizard.

## Reasoning

Manual LXC creation would mostly repeat container-creation mechanics (`pct create`, template selection, resource allocation) already being practiced through the Windows 11 and Ubuntu Server VM builds — the marginal learning value of doing it again, for a slightly different tool (LXC vs. full VM), was judged to be low. Career interest is oriented toward sysadmin/cloud/networking depth rather than repeating setup mechanics for their own sake.

The actual learning goal for this project was understanding Pi-hole and DNS-level ad blocking itself — how it intercepts and filters DNS queries, static IP requirements for a service every device depends on, and the router-side DHCP/DNS configuration needed to make it network-wide. None of that is skipped by using the helper script; it still requires making every real decision (static IP, resource sizing, IPv6 handling, FUSE/TUN support) through the wizard's prompts, just without hand-typing the container-creation commands.

Community helper scripts of this kind are also normal, widely-used tooling in the homelab community generally — not considered a shortcut that undermines the result.

## Status

Adopted for the Pi-hole build (LXC VMID 102). Manual `pct create` workflows remain the default assumption for VM-equivalent builds where the container-creation mechanics themselves are the point.

## Related

- [Proxmox](../Projects/Proxmox.md)
