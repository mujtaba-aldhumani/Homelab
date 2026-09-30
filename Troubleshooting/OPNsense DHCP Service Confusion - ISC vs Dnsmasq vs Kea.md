DHCP not working on new VLANs — wrong service assumed, then wrong scope

## Symptom

After creating two VLAN interfaces (Servers/10, Clients/20) in OPNsense and configuring DHCP ranges for each, a test LXC container on VLAN 10 sent `DHCPDISCOVER` broadcasts (confirmed with `dhclient -v eth0`) but received no `DHCPOFFER` at all — not even a rejection, just silence.

## First (wrong) diagnosis

Assumed the DHCP configuration lived under the classic ISC DHCP service (`Services → DHCPv4`), and assumed the firewall's port dropdown for the DHCP allow rule would have a named `DHCP` or `BOOTPS` preset. Neither assumption was correct, and both cost real troubleshooting time before being caught:

- OPNsense 26.7 has **no ISC DHCPv4 service at all** — it's end-of-life and was removed from the product, not just deprecated in the UI. The active DHCP server on a current install is **Dnsmasq DNS & DHCP** (default since 25.7) or **Kea DHCP**, configured on entirely different Services pages. The page that was actually being used the whole time (per-interface range editor) belonged to Dnsmasq, organized similarly enough to the old ISC layout to cause the confusion.
- The firewall rule's **Destination Port** field is a fixed-list selector, not a free-text or "type to add" field — typing `67` directly returns "No results matched," and there's no built-in `DHCP`/`BOOTPS` preset in that list. The fix was creating a **Port alias** (Firewall → Aliases, type `Port(s)`, content `67`) and selecting that alias in the rule instead.

## Real root cause

Even after the DHCP ranges (Dnsmasq → DHCP ranges) and the firewall allow rule (UDP, port 67 via alias, on both VLAN interfaces) were correct, DHCP still didn't work. The actual cause: **Dnsmasq's own interface scope** (Services → Dnsmasq DNS & DHCP → General → Interfaces) was set to `LAN` only. The Servers and Clients VLANs were never added to that list, so the Dnsmasq daemon simply wasn't listening on those interfaces at all — no amount of correct ranges or firewall rules would matter, since the service itself wasn't bound there.

## Diagnosis path

1. Confirmed the container's own DHCP client config and behavior were correct (`cat /etc/network/interfaces`, then `dhclient -v eth0` showing active `DHCPDISCOVER` broadcasts with no response) — ruled out the client side entirely.
2. Fixed the firewall rule (alias workaround) and re-tested — still no offer.
3. Checked **Services → Kea DHCP** and confirmed it was disabled with no interfaces selected — ruled Kea out.
4. Checked **Services → Dnsmasq DNS & DHCP → General** and found **Interfaces: LAN** only, with Servers and Clients absent from the multi-select.

## Fix

Added `Servers` and `Clients` to Dnsmasq's Interfaces multi-select (alongside `LAN`), saved, and restarted the service. `dhclient -v eth0` on the test container immediately returned a normal `DHCPOFFER` → `DHCPREQUEST` → `DHCPACK` sequence.

## Lesson

A service can be "configured" (correct ranges, correct rules referencing it) while still not actually running where it's needed. Check every layer that has to be true at once — service enabled, service scoped to the right interface, ranges correct, firewall allowing the traffic — rather than assuming that because most of the configuration is correct, all of it is.
