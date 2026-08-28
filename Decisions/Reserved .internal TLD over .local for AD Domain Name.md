Reserved .internal TLD over .local for AD Domain Name

## Decision

Named the Active Directory forest/domain `mujtaba.internal`, using the IANA-reserved `.internal` special-use TLD, rather than the more commonly-tutorialized `.local` or a subdomain of a real owned public domain.

## Context

The AD domain name becomes the root of the forest's namespace and the DNS zone the domain controller hosts — baked into every object's identity, Kerberos realm name, and how future clients locate the domain. Changing it later is disruptive, so it needed to be decided deliberately rather than defaulted into.

## Reasoning

- A real public domain not owned by the account holder risks split-brain DNS if that name is ever actually registered or resolved externally.
- `.local` is technically functional but is also used by mDNS/Bonjour (Apple zero-config networking), which can cause resolution conflicts, especially with mixed-OS clients — Microsoft has moved away from recommending it.
- `.internal` is an IANA-reserved special-use TLD specifically intended for private, non-routed namespaces like this one — no collision risk, and a legitimate modern alternative to `.local`.
- The enterprise-standard alternative (a subdomain of a real owned domain, e.g. `ad.company.com`) was considered but not used, since this is a personal lab with no owned public domain backing it.

## Status

Adopted.

## Related

- [Proxmox](../Projects/Proxmox.md)
