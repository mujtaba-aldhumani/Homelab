Isolated Virtual Bridge over Changing the Home Network

## Decision

Built the segmentation practice lab on a new, isolated Proxmox bridge (`vmbr1`, no physical port) instead of adding VLANs (Virtual Local Area Networks) to the real home network.

## Context

The home network runs on a Spectrum modem and Google Wifi, which doesn't support VLANs, and it carries around 75 devices belonging to other people. Practicing segmentation and firewall rules means making mistakes, and a mistake on the real network could cut off shared devices or the other lab guests.

## Reasoning

- A bridge with no physical port can't reach the home network or the internet unless it is deliberately connected, so mistakes stay contained (a small "blast radius")
- Nothing on the existing bridge (`vmbr0`) or its guests changes, so the working lab projects are not at risk
- A second DHCP (Dynamic Host Configuration Protocol) server for the sandbox can't conflict with Google Wifi's DHCP, because the two networks never touch except through the firewall
- Cost: this is virtual-only segmentation on a single host, with no physical switch involved

## Details

- Bridge: `vmbr1`, VLAN aware, `bridge-ports none`, `bridge-vids 2-4094`
- Existing bridge `vmbr0` (`192.168.86.200/24` on `nic0`) left untouched
