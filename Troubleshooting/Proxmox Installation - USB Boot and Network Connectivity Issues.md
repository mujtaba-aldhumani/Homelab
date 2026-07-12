## Issue

Two separate problems encountered during Proxmox VE installation on the Lenovo ThinkCentre M720q:

1. Initial boot attempt from USB installer failed with: `[ERROR] no device with valid iso found, please check your installation medium`
2. After a successful install, the Proxmox web UI (`https://192.168.86.200:8006`) was unreachable — browser showed a connection timeout, and `ping 192.168.86.200` returned 100% packet loss from another device on the same network

## Environment

- Lenovo ThinkCentre M720q (Proxmox host)
- 64GB USB flash drive, written using USB Imager
- Proxmox VE 9.2-1 ISO, checksum-verified against official SHA256 before writing
- Windows 11 machine used for network testing (`ping`, `ipconfig`, `arp -a`)

## Root Cause Analysis

**Issue 1 — ISO not found**

Root cause was not conclusively isolated to one factor, but the working theory is a bad or incomplete write to the USB drive on the first attempt. The ISO's checksum was verified as correct beforehand, ruling out a corrupted download. Re-writing the same verified ISO to the same USB drive with the same tool (USB Imager) resolved the issue on the second attempt.

**Issue 2 — Web UI unreachable after install**

Root cause was a stale network/boot state following the install's automatic reboot. Sequence of events:

- After installation completed, the machine rebooted directly back into the USB installer environment (USB was still inserted, boot order prioritized USB over the internal NVMe drive)
- After removing the USB and rebooting, the system booted into Proxmox correctly and displayed the expected console welcome screen with the correct IP
- Despite the console showing correct network info, the web UI was still unreachable and ICMP pings to the host timed out completely, while pings to the gateway from the same testing device succeeded
- A second full restart of the Proxmox host (not the USB removal — an actual reboot of the already-installed system) resolved the issue; the host became pingable and the web UI became reachable immediately after

## Resolution Steps

1. Re-verified ISO integrity via SHA256 checksum against the official Proxmox download page
2. Re-wrote the USB installer using USB Imager with the same verified ISO
3. Successfully booted the installer and completed installation
4. Powered off, physically removed the USB drive, powered back on
5. Confirmed Proxmox boot to console with correct IP displayed
6. Diagnosed the unreachable web UI by isolating variables in order:
    - Confirmed the client device could reach the gateway (`ping 192.168.86.1` succeeded)
    - Confirmed the client device could not reach the Proxmox host (`ping 192.168.86.200` timed out)
    - Re-checked the Proxmox console to confirm the IP matched what was configured
7. Performed a full restart of the Proxmox host itself
8. Confirmed ping and web UI access both worked immediately after

## Validation

- `ping 192.168.86.200` succeeded from a separate device on the same subnet
- `https://192.168.86.200:8006` loaded the Proxmox login page
- Successfully logged in, applied updates, and switched to the no-subscription repo

## Lessons Learned

- A "no valid ISO found" error doesn't necessarily mean a bad download — verify checksum first, but if that passes, re-writing the USB is a reasonable next troubleshooting step before assuming the ISO itself is the problem
- After a fresh install, don't assume the first reboot will land you in the new OS — check whether the boot device is still inserted and whether boot order actually prioritizes the internal drive yet
- A correctly displayed IP on the console doesn't guarantee the network stack is fully functional — isolate by testing the gateway first, then the host, from an external device
- When a freshly rebooted service appears networked (correct IP shown) but is still unreachable, a clean restart of the _installed_ system (not just removing install media) can clear a stale state that persisted from the install/reboot cycle

## Future Improvements

If this happens again on a future node, check `ip addr` and `systemctl status pveproxy` directly from the Proxmox console before assuming a full restart is necessary — this would help confirm whether the issue is at the network interface level or the web service level, and build a faster diagnostic habit instead of defaulting straight to a reboot.