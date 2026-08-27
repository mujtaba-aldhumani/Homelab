Disabling IPv6 over Public IPv6 DNS Fallback for Google Wifi Custom DNS

## Decision

Disabled IPv6 entirely on the Google Wifi network, rather than entering a public IPv6 DNS address (e.g. Google's `2001:4860:4860::8888`) to satisfy Google Wifi's custom-DNS form requirement.

## Context

Google Wifi's custom DNS settings page requires at least one valid IPv6 DNS entry before it will save a custom config, even though the actual goal was only to point IPv4 DNS at Pi-hole (`192.168.86.202`, which itself has IPv6 disabled). Entering a public IPv6 DNS server would satisfy the form, but any device that picks up an IPv6 address could then resolve DNS directly over IPv6 to that public server, bypassing Pi-hole — and its ad-blocking/logging — entirely for that traffic.

## Reasoning

Disabling IPv6 network-wide closes that bypass path outright: with no IPv6 addresses handed out, there's nothing to resolve over IPv6, so all DNS traffic is forced through the IPv4 config pointed at Pi-hole. This was simpler and more thorough than the alternative of enabling IPv6 on the Pi-hole container itself, which would require rebuilding its network config for a problem the network doesn't otherwise need IPv6 to solve.

## Alternatives Considered

- Public IPv6 DNS (e.g. `2001:4860:4860::8888`) as a stopgap — rejected; leaves an IPv6 bypass path around Pi-hole.
- Enabling IPv6 on the Pi-hole LXC and pointing IPv6 DNS at it — more correct long-term, but more setup work than needed right now; noted as a possible future improvement alongside Unbound.

## Status

Adopted.

## Related

- [Proxmox](../Projects/Proxmox.md)
