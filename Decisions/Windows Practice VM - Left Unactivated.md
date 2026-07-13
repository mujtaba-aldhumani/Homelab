Windows Practice VM - Left Unactivated

## Decision

Left the Windows 11 practice VM (VMID 100) unactivated rather than pursuing further activation troubleshooting.

## Context

Signed into the Microsoft account that the OEM Windows digital license had been linked to (a dedicated account made for this machine, separate from personal accounts). Activation still failed with error `0xC004F213` — "no product key found."

![[windows11-activation-error.png]]
*Activation status showing "Not active," error 0xC004F213.*

## Reasoning

A Windows digital license is tied to both a Microsoft account and a hardware fingerprint of the specific machine it was activated on. This VM's virtual hardware (as presented by Proxmox/QEMU) doesn't match the original physical ThinkCentre's fingerprint, so Microsoft's activation servers don't recognize it as the same device — confirmed via the "Unable to activate Windows" screen, which stated no linked devices could be found for reactivation.

![[windows11-activation-unable-reactivate.png]]
*Troubleshoot flow result — no linked devices found eligible for reactivation.*

Since this VM is a practice/learning environment rather than a daily-use machine, the downsides of running unactivated (a watermark, some locked personalization settings) were judged not worth chasing further.

## Alternatives Considered

Manually entering the saved OEM product key (Settings → Activation → Change product key) was noted as a possible path if activation is wanted later, since that doesn't rely on the hardware-fingerprint link the way the digital license does. Not pursued for now.
