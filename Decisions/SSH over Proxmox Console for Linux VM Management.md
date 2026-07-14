SSH over Proxmox Console for Linux VM Management

## Decision

Manage the Ubuntu Server VM (`tailscaleproxy`) via SSH from a terminal on the management laptop, rather than Proxmox's built-in noVNC web console.

## Context

Ran into a practical problem trying to copy/paste commands into the Proxmox web console for the Ubuntu VM — the noVNC console doesn't support clipboard sharing between host and guest at all, making it painful to run anything beyond very short manually-typed commands.

## Reasoning

Considered SPICE (Proxmox's alternative console protocol, which does support clipboard sharing) as a fix, but it requires extra setup on both ends: switching the VM's display device to SPICE (qxl), installing spice-guest-tools/spice-vdagent inside the guest, and using a separate client app (virt-viewer/remote-viewer) instead of the browser.

For a headless Linux server VM, none of that is actually necessary — OpenSSH was already installed during the Ubuntu Server setup. SSH from Windows Terminal or PowerShell supports normal copy/paste natively, requires no extra configuration on the VM, and is also just the realistic day-to-day workflow for managing a Linux server — sysadmins essentially never touch a hypervisor's console for routine work on a Linux box. The console is a fallback for when networking itself is broken and SSH isn't reachable, not the primary way to work.

(Note: this reasoning is specific to headless Linux VMs. The Windows practice VM, being GUI-based, would use RDP instead for the equivalent clipboard-sharing benefit, if remote management of it is ever needed.)

## Status

Adopted going forward for the `tailscaleproxy` VM. All Dante and Tailscale configuration work was done over SSH from a PowerShell/Windows Terminal session on the management laptop.
