Stremio Has No Native Proxy Support (Config Field Misidentified)

## Symptom

Planned to configure Stremio to route its traffic through the Dante SOCKS5 proxy running on the `tailscaleproxy` VM, expecting a proxy field somewhere in Stremio's settings. When actually checking the app, the only relevant-looking options under Settings → Streaming were a "URL" field and a "Streaming HTTPS endpoint" toggle (shown disabled) — and the HTTPS endpoint option wasn't even present on the TV app version at all.

## Investigation

Rather than assume those fields were just a differently-labeled proxy setting, searched for how Stremio's proxy support actually works. Found:

- Stremio's own bug tracker (`Stremio/stremio-bugs` issue #1869) describes a user working around the *lack* of proxy support by using third-party tools like Proxifier/ProxyCap to force the Stremio executable through a local SOCKS5 proxy at the OS level — implying no built-in support exists.
- Stremio's feature-request tracker (`Stremio/stremio-features` issue #353) has an open request specifically asking for SOCKS5 proxy support to be added — confirming it isn't currently a feature.
- The "URL"/"Streaming HTTPS endpoint" settings actually configure the connection between the Stremio client and Stremio's own local streaming server (the component that handles torrent/debrid playback), which is a completely different thing from an external network proxy.

## Root Cause

Incorrect initial assumption that Stremio had proxy configuration in its settings menu, based on general expectation rather than checking the actual app or verifying against Stremio's documentation/issue tracker first.

## Resolution

Since routing Stremio's own app traffic through Dante isn't possible without extra tooling that doesn't work on a TV app anyway, re-scoped the actual goal: rather than proxy the app, proxy the underlying debrid stream URL (via an addon like Comet or MediaFlow Proxy) — or, simpler still, use Tailscale's exit-node feature at the OS level once the real requirement turned out to be just two specific devices rather than an open-ended group. See [Tailscale Exit Node over SOCKS5 Proxy for Stremio-RD](../Decisions/Tailscale%20Exit%20Node%20over%20SOCKS5%20Proxy%20for%20Stremio-RD.md) for the follow-up decision.

## Takeaway

Verify a specific app's actual feature support (official docs, issue tracker, changelog) before designing infrastructure around an assumption of what it "should" support — especially for anything involving proxying, since that's a common feature gap in consumer streaming apps.
