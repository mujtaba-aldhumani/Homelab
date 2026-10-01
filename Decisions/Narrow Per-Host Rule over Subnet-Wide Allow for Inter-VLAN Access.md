Narrow Per-Host Rule over Subnet-Wide Allow for Inter-VLAN Access

## Decision

The first inter-VLAN firewall rule (Clients → Servers) was scoped to one specific host (`10.10.10.112/32`, `server-test`'s address) rather than the whole Servers subnet (`10.10.10.0/24`).

## Context

`server-test` was the only host on the Servers VLAN that needed to be reached, and it was only reached on one port (HTTP, 80).

## Reasoning

- Least privilege: a rule should permit exactly what's needed, not the broadest match that happens to work. Allowing the whole subnet would have passed the same test just as well, but would also have silently permitted traffic to any future host added to that VLAN, without that access ever being a deliberate decision
- A per-host rule is self-documenting: reading it later shows exactly which host and service it was written for, rather than requiring the reader to know why "the whole subnet" was considered acceptable
- Mirrors the same reasoning already applied to the AD helpdesk delegation (scoped to one task, not broad admin rights) — consistent practice across projects rather than a one-off choice

## Trade-off

If more hosts are added to the Servers VLAN later needing the same kind of access, each will need its own rule (or a purpose-built alias/group), rather than being covered automatically. Accepted as the right trade-off for a lab meant to demonstrate precise, auditable rules.
