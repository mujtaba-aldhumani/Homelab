Tailscale Proxy Approach for Stremio-RD

## Decision

Use a dedicated proxy service (SOCKS5/HTTP) running on top of Tailscale on the Ubuntu VM, rather than Tailscale's built-in exit-node feature, to share one IP address for a Stremio + Real-Debrid setup among multiple people.

## Context

Goal: let multiple people run Stremio with Real-Debrid through a single shared IP address, since RD can flag/ban accounts used from multiple IPs at once. At the same time, only Stremio/RD traffic should be shared — each person's other traffic (browsing, other apps) should stay on their own connection.

## Reasoning

Tailscale's exit-node feature is all-or-nothing per device — a device connected to an exit node routes its *entire* internet connection through that node, not just selected apps. That doesn't fit the requirement of leaving everything except Stremio/RD on each person's own connection.

A separate proxy service running on the Ubuntu VM, reachable via its Tailscale IP, solves this instead: each person configures Stremio's own proxy setting to point at that address. Only Stremio's traffic goes through the tunnel this way; everything else on their device is unaffected.

## Known Limitation

Even with all Stremio/RD traffic funneled through one IP, Real-Debrid may also track concurrent stream count per account independent of IP address. Single-IP routing may reduce IP-based flags but might not fully prevent restrictions if multiple people stream simultaneously.

## Status

**Superseded on 2026-07-13** — see [[Tailscale Exit Node over SOCKS5 Proxy for Stremio-RD]] for the revised decision and reasoning. Dante was fully installed and confirmed working (SOCKS5 proxy, dedicated no-shell auth user), but Stremio turned out to have no native proxy support, and the actual requirement narrowed to two specific devices — both of which support Tailscale's exit-node feature natively. Dante remains installed on the VM in case a broader multi-user shared-proxy need comes up later.
