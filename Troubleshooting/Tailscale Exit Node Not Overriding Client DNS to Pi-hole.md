Tailscale Exit Node Not Overriding Client DNS to Pi-hole

## Symptom

While away from home and connected through the `tailscaleproxy` exit node, Pi-hole's official test domain (`ads-tracking.pi-hole.net`) still loaded normally and no ads were blocked. Happened identically on both an iPhone and a laptop, ruling out a single-device cause.

## Diagnosis

Checked device-side DNS overrides first, since two different devices failing identically pointed away from a single-device fluke: ruled out iCloud Private Relay (off) and browser DNS-over-HTTPS (confirmed off in Edge's settings) as causes. Checked Pi-hole's live Query Log next while retesting — the log showed steady traffic, but every entry was from `192.168.86.1` (other devices already on the home LAN, e.g. Roblox/Fire TV traffic); the laptop's test query never appeared in the log at all, meaning it never reached Pi-hole in the first place.

## Root Cause

Turning on a Tailscale exit node reroutes a device's general internet traffic through that exit node, but it does not by itself change which DNS server the device queries — those are two separate settings. In the Tailscale admin console's DNS page, `192.168.86.202` was already listed as a nameserver, but the global "Override DNS servers" toggle was off, and the nameserver's own "Use with exit node" toggle was also off — so devices kept using their default DNS resolver even while tunneling their traffic through the exit node.

## Fix

In the Tailscale admin console (DNS page): enabled the global "Override DNS servers" toggle, and on the `192.168.86.202` nameserver entry, enabled "Use with exit node." Left "Restrict to domain" (Split DNS) off, since all DNS traffic — not just specific domains — should go through Pi-hole. Re-tested afterward: the laptop's Tailscale IP appeared in Pi-hole's query log, and the test domain was correctly blocked.

## Takeaway

An active Tailscale exit node does not imply DNS is also being overridden — they're independent settings that both need to be turned on for a device to actually use a remote DNS server (like Pi-hole) while tunneling through an exit node. When remote testing shows no ad blocking at all (not even a partial block rate), check whether queries are reaching Pi-hole in its Query Log before assuming the blocklist or Pi-hole itself is at fault.
