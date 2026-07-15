Unbound Deferred as a Separate Follow-Up Project

## Decision

Declined the Pi-hole installer's optional prompt to install Unbound (a recursive DNS resolver) alongside Pi-hole, for now.

## Context

Partway through the Pi-hole install, the installer asked: "Would you like to add Unbound?" Unbound would let the Pi-hole container resolve DNS queries directly against root DNS servers itself, rather than forwarding non-blocked queries to an upstream provider (e.g. Google or Cloudflare DNS) as Pi-hole does by default.

## Reasoning

Unbound is a genuinely valuable upgrade — better DNS privacy (no single upstream provider sees the household's full browsing history via DNS) and no dependency on a third party's DNS server being available — but it's a distinct concept (recursive resolution vs. simple forwarding) layered on top of the core goal for this session, which was getting basic network-wide ad blocking working end-to-end. Bundling it into the same install risked more troubleshooting surface area on a night that already had plenty, and diluting the primary goal.

## Status

Deferred, not rejected. Planned as a deliberate future project once the core Pi-hole setup is proven stable and the network-wide DNS switch (see [Proxmox](../Projects/Proxmox.md)) is confirmed working.

## Related

- [Proxmox](../Projects/Proxmox.md)
