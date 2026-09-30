## Goal

Build an isolated virtual sandbox inside Proxmox to practice network segmentation and firewall policy. A firewall (OPNsense, an open source firewall and router) sits between two VLAN (Virtual Local Area Network) groups of guests, and firewall rules control exactly which traffic is allowed between them, with every rule proven by a test. This is the networking slot in the career-exploration rotation — see [Documentation & Planning](Documentation%20&%20Planning.md).

## Design

- Isolated bridge `vmbr1` for the sandbox (no physical port, VLAN aware)
- OPNsense VM (105): WAN NIC on `vmbr0`, LAN NIC on `vmbr1`, both VirtIO, no VLAN tag at the Proxmox level (OPNsense handles VLAN tagging itself once VLANs are configured)
- Two VLANs planned on the sandbox: one for servers, one for clients, each its own subnet with its own DHCP scope
- Default-deny firewall rules between the VLANs, with only needed traffic allowed and every rule tested
- Lightweight LXC guests as test machines, to save RAM

## Build

### Step 1 — Isolated sandbox bridge (2026-09-28)

- Created Linux bridge `vmbr1` in the Proxmox GUI: no IPv4 address, no bridge ports, VLAN aware, comment `Isolated Sandbox`
- Verified in `/etc/network/interfaces` — `bridge-ports none`, `bridge-vlan-aware yes`, `bridge-vids 2-4094`
- Existing bridge `vmbr0` (`192.168.86.200/24`, gateway `192.168.86.1`, port `nic0`) unchanged
- Design choice recorded in [Isolated Virtual Bridge over Changing the Home Network](../Decisions/Isolated%20Virtual%20Bridge%20over%20Changing%20the%20Home%20Network.md)

### Step 2 — OPNsense installer verified and uploaded (2026-09-28/29)

- Downloaded OPNsense 26.7 `dvd-amd64.iso.bz2`; verified SHA256 against the published checksum file before use
- First download was the `vga` image (wrong type for a Proxmox virtual CD-ROM — that format is meant for writing to a physical USB stick); caught and corrected before building the VM
- Unpacked and uploaded the verified `.iso` to Proxmox local storage

### Step 3 — OPNsense VM built and installed (2026-09-29)

- Created VM 105 (`opnsense`): 2 vCPU, 2GB RAM, 20GB disk, SeaBIOS
- Network: `net0` on `vmbr0` (WAN), `net1` on `vmbr1` (LAN), both VirtIO, no VLAN tags
- Installed OPNsense 26.7 from the verified ISO; set a new root password from the console (replacing the installer default)
- Interface assignment initially came out reversed (LAN mapped to `vtnet0`/`vmbr0`, WAN to `vtnet1`/`vmbr1`) because the installer assigns roles in NIC detection order with no knowledge of which Proxmox bridge each NIC is wired to; corrected via console option 1 (Assign interfaces) to WAN=`vtnet0`, LAN=`vtnet1`
- WAN obtained a DHCP address from the home network (`192.168.86.116/24`); LAN kept its default `192.168.1.1/24`

### Step 4 — GUI access confirmed (2026-09-29)

- Built a small Debian LXC (`lantest`) on `vmbr1`, no VLAN tag, to reach OPNsense's LAN-only web GUI
- Confirmed DHCP lease from OPNsense (`192.168.1.137/24`) and confirmed the login page over HTTPS with `curl -k`
- Added a second NIC (`vmbr1`, no tag) to the existing Windows 11 VM (100) to get a browser onto the sandbox and reach the OPNsense dashboard directly; logged in successfully

### Step 5 — Two VLANs created and assigned (2026-09-30)

- Created VLAN 10 (`Servers`) and VLAN 20 (`Clients`) under Interfaces → Devices → VLAN, both with parent `vtnet1` (the LAN trunk)
- Assigned both as usable interfaces under Interfaces → Assignments (`opt1`/Servers, `opt2`/Clients); creating a VLAN device alone does not make it a usable interface — it must be separately assigned
- Set static IPs: Servers `10.10.10.1/24`, Clients `10.10.20.1/24`

### Step 6 — DHCP and default-deny firewall rules (2026-09-30)

- Discovered OPNsense 26.7 has no ISC DHCPv4 service at all (it's end-of-life/removed); the active DHCP server on this install is **Dnsmasq DNS & DHCP**, configured per-interface similarly to the old ISC pages — see [OPNsense DHCP Service Confusion - ISC vs Dnsmasq vs Kea](../Troubleshooting/OPNsense%20DHCP%20Service%20Confusion%20-%20ISC%20vs%20Dnsmasq%20vs%20Kea.md)
- Configured DHCP ranges for both VLANs in Dnsmasq: Servers `10.10.10.100`–`199`, Clients `10.10.20.100`–`199`
- Added a default-deny-compatible allow rule on each VLAN interface (UDP, source `any`, destination `any`, destination port via a new `DHCP_Server_Port` alias for port 67) to let DHCP broadcasts reach the firewall — required because every new interface starts with zero rules, including for traffic destined to OPNsense's own services
- DHCP still failed after the rules and ranges were correct, because Dnsmasq's own **Interface** selector (Services → Dnsmasq DNS & DHCP → General) was scoped to `LAN` only — the Servers and Clients VLANs were never added to it, so the service wasn't listening there regardless of any other config; added both and restarted the service — full diagnosis in [OPNsense DHCP Service Confusion - ISC vs Dnsmasq vs Kea](../Troubleshooting/OPNsense%20DHCP%20Service%20Confusion%20-%20ISC%20vs%20Dnsmasq%20vs%20Kea.md)
- Confirmed working DHCP on `server-test` (LXC, VLAN 10): `dhclient -v eth0` returned a lease (`10.10.10.112`)

## Security Takeaways

- **Isolation and blast radius:** a bridge with no physical port cannot reach the home network or the internet, so mistakes inside the sandbox cannot affect real devices.
- **Changing default credentials:** the OPNsense live installer ships a known default login (`installer`/`opnsense`), and a fresh install of the OS itself still defaults to a known root password until explicitly reset. Leaving default credentials in place on anything network-facing is one of the most common real-world breach causes.
- **Bastion/jump-host model:** OPNsense's web GUI is reachable only from the trusted LAN side by design, not from WAN. Management interfaces exposed to an untrusted network are a common real attack surface; reaching them only through a specific, controlled path (here, a VM or container deliberately attached to LAN) is the underlying principle.
- **Verifying assumptions instead of guessing:** the WAN/LAN mismatch was diagnosed by cross-referencing MAC addresses between Proxmox's Hardware tab and OPNsense's own interface list, rather than assuming either side was "obviously" correct.
- **Least privilege for firewall rules:** the DHCP allow rule was scoped to exactly one protocol (UDP) and one port (67 via a named alias), not a blanket allow — the same principle as narrowing an AD delegation to only what's needed.
- **A service being "configured" isn't the same as a service being active where you need it:** Dnsmasq had correct DHCP ranges and correct firewall rules, but was never told to actually listen on the new VLAN interfaces. Every layer (enabled, scoped to the right interface, correctly ranged, allowed by firewall) has to hold at once.

## Status

In progress. Isolated bridge, OPNsense VM, GUI access, both VLANs, and working DHCP on both interfaces are done and verified. Firewall rules between the VLANs (beyond the DHCP allow rules) are not yet written or tested.

## Related Decisions

- [Isolated Virtual Bridge over Changing the Home Network](../Decisions/Isolated%20Virtual%20Bridge%20over%20Changing%20the%20Home%20Network.md)

## Related Troubleshooting

- [OPNsense WAN/LAN Interfaces Assigned Backwards](../Troubleshooting/OPNsense%20WAN-LAN%20Interfaces%20Assigned%20Backwards.md)
- [OPNsense DHCP Service Confusion - ISC vs Dnsmasq vs Kea](../Troubleshooting/OPNsense%20DHCP%20Service%20Confusion%20-%20ISC%20vs%20Dnsmasq%20vs%20Kea.md)

## Project Log

### 2026-09-28

- Created and verified the isolated `vmbr1` bridge

### 2026-09-29

- Verified and uploaded the OPNsense installer ISO (after catching a wrong image-type download)
- Built and installed the OPNsense VM (105); corrected reversed WAN/LAN interface assignment
- Confirmed web GUI access via a temporary LXC and via the Windows 11 VM

### 2026-09-30

- Created and assigned both VLANs (Servers/10, Clients/20) with static IPs
- Diagnosed and fixed DHCP: found the active DHCP service is Dnsmasq (not the deprecated ISC DHCP), added a least-privilege allow rule for DHCP traffic on each VLAN via a port alias, and corrected Dnsmasq's interface scope to actually include the new VLANs
- Confirmed a working DHCP lease on a test container on VLAN 10

## Next Steps

1. Build a second test container on VLAN 20 (Clients) and confirm its DHCP lease
2. Ping between the two test containers to confirm default-deny is blocking all traffic
3. Write and test a narrow allow rule between the VLANs, proving both the pass and the deny cases
