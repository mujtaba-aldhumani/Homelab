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

Ubuntu VM and Tailscale connectivity are set up and confirmed working. The actual proxy service (SOCKS5/HTTP) has not yet been installed/configured — planned as a next step.
