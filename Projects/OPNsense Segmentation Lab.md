## Goal

Build an isolated virtual sandbox inside Proxmox to practice network segmentation and firewall policy. A firewall (OPNsense, an open source firewall and router) will sit between two VLAN (Virtual Local Area Network) groups of guests, and firewall rules will control exactly which traffic is allowed between them, with every rule proven by a test. This is the networking slot in the career-exploration rotation — see [Documentation & Planning](Documentation%20&%20Planning.md).

## Planned Design

- Isolated bridge `vmbr1` for the sandbox (no physical port), VLAN aware
- OPNsense VM with its WAN (Wide Area Network, the "outside" side) on `vmbr0` and its LAN (Local Area Network, the "inside" side) on `vmbr1`
- Two VLANs on the sandbox: one for servers and one for clients, each its own subnet with its own DHCP (Dynamic Host Configuration Protocol, the service that hands out IP addresses) scope
- Default-deny firewall rules between the VLANs, with only the traffic that is needed allowed
- Lightweight LXC (Linux Container) guests as the test machines, to save RAM (Random Access Memory)

## Build

### Step 1 — Isolated sandbox bridge (2026-09-28)

- Created a Linux bridge `vmbr1` in the Proxmox GUI (Graphical User Interface): no IPv4 address, no bridge ports, VLAN aware, comment `Isolated Sandbox` — see [Isolated Virtual Bridge over Changing the Home Network](../Decisions/Isolated%20Virtual%20Bridge%20over%20Changing%20the%20Home%20Network.md)
- Verified the generated configuration in `/etc/network/interfaces`:

```
auto vmbr1
iface vmbr1 inet manual
        bridge-ports none
        bridge-stp off
        bridge-fd 0
        bridge-vlan-aware yes
        bridge-vids 2-4094
#Isolated Sandbox
```

- The existing bridge `vmbr0` (`192.168.86.200/24`, gateway `192.168.86.1`, port `nic0`) is unchanged

## Security Takeaways

- **Isolation and blast radius:** a bridge with no physical port cannot reach the home network or the internet, so mistakes made inside the sandbox cannot affect real devices. Blast radius is how much damage a mistake or an attack can cause; isolation keeps it small.

## Status

In progress — Step 1 complete (isolated bridge created and verified).

## Related Decisions

- [Isolated Virtual Bridge over Changing the Home Network](../Decisions/Isolated%20Virtual%20Bridge%20over%20Changing%20the%20Home%20Network.md)

## Project Log

### 2026-09-28

- Created and verified the isolated `vmbr1` bridge — [Daily Log — 2026-09-28](../Daily%20Logs/2026-09-28.md)

## Next Steps

1. Download and verify the OPNsense installer, upload it to Proxmox, and build the OPNsense VM
2. Create the two VLANs with DHCP, add the test containers, then write and test the firewall rules
