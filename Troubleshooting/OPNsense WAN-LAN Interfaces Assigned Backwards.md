WAN and LAN interfaces assigned backwards on first OPNsense install

## Symptom

After installing OPNsense (VM 105) and rebooting into the console menu, the interface assignment showed:

```
LAN (vtnet0)  -> v4: 192.168.1.1/24
WAN (vtnet1)  -> (no address)
```

`vtnet0` is the Proxmox VM's first virtual NIC, wired to `vmbr0` (the real home network). `vtnet1` is the second NIC, wired to `vmbr1` (the isolated sandbox). This meant OPNsense had the roles reversed: the home-network-facing NIC was labeled LAN (trusted/protected), and the sandbox-facing NIC was labeled WAN (untrusted, default-blocked).

## Cause

The OPNsense installer assigns WAN/LAN based on NIC detection order alone — it has no way to know which Proxmox bridge each virtual NIC is actually wired to. That mapping only exists in Proxmox's own configuration (the Hardware tab), so it has to be supplied manually during interface assignment.

## Diagnosis

Ran `ifconfig -a | grep flags` in the OPNsense shell (console option 8) to list all interfaces. `vtnet0` showed `UP, RUNNING`; `vtnet1` showed neither, since it had not yet been assigned a role. This confirmed both NICs existed and were detected — the problem was assignment, not missing hardware. Cross-referenced the MAC address shown for `vtnet1` against the MAC listed for `net1` in Proxmox's VM 105 Hardware tab to confirm which physical bridge (`vmbr1`) it corresponded to.

## Fix

From the console menu, option 1 (Assign interfaces) → answered `n` to LAGGs and VLANs → assigned WAN to `vtnet0`, LAN to `vtnet1`. Confirmed the reassignment. WAN then obtained a DHCP address from the home network; LAN kept its default `192.168.1.1/24`.

## Lesson

Don't assume an installer's automatic role assignment matches the underlying virtual hardware layout — verify by cross-referencing identifiers (MAC addresses) between the hypervisor and the guest OS rather than assuming either side is self-evidently correct.
