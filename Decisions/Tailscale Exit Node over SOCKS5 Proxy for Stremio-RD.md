Tailscale Exit Node over SOCKS5 Proxy for Stremio-RD

## Decision

Use Tailscale's built-in exit-node feature on the brother's Google TV and personal iPhone, rather than the previously-built Dante SOCKS5 proxy ([[Tailscale Proxy Approach for Stremio-RD]]), to share the home IP for the Stremio + Real-Debrid use case.

## Context

The original decision assumed an open-ended group of friends who each needed only Stremio's traffic shared, with everything else on their own connection — which ruled out exit nodes (all-or-nothing per device) and led to building a dedicated SOCKS5 proxy (Dante) instead.

Two things changed that assumption:

1. **Stremio has no native proxy support.** Investigation (checking the app's actual Settings menu, then confirming via Stremio's own GitHub issue tracker) showed the official client has no SOCKS5/HTTP proxy configuration at all. The "Streaming HTTPS endpoint" setting visible in the app configures the connection to Stremio's local streaming server, not an external proxy. Workarounds like Proxifier exist for Windows desktop only, and don't extend to a TV app — so pointing Stremio at Dante directly wasn't actually achievable without extra layers (e.g. a debrid-proxy addon like Comet or MediaFlow Proxy sitting in front of Dante).
2. **The real scope is narrow.** Only two specific devices need this: the brother's Google TV and a personal iPhone — not an arbitrary group of friends. Both platforms support Tailscale's exit-node feature natively.

## Reasoning

Given the narrowed scope, the downside of exit nodes (routing *all* traffic, not just Stremio's) becomes a manageable trade-off rather than a blocker:

- **Google TV** almost certainly only runs streaming apps, so routing all of its traffic through the home IP has no real downside.
- **iPhone** does carry a real trade-off (browsing, all apps would route through the home connection while enabled) — mitigated by toggling the exit node on only while actively using Stremio away from home, and off otherwise. Android supports per-app split tunneling (would avoid this trade-off entirely), but iOS does not.

This avoids the added complexity of a debrid-proxy addon layer entirely, at the cost of coarser (device-level rather than app-level) traffic control.

## Known Limitation (carried over)

Real-Debrid may track concurrent stream count per account independent of IP address, so this doesn't fully guarantee no restrictions if both people stream simultaneously.

## Status

`tailscaleproxy` VM advertised and approved as both a subnet router and an exit node. Dante remains installed and functional on the VM as a fallback if a broader (non-two-person) shared-proxy need comes up later — see [[Tailscale Proxy Approach for Stremio-RD]] for that original setup.
