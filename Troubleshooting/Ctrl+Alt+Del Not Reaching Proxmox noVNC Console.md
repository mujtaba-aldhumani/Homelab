Ctrl+Alt+Del Not Reaching Proxmox noVNC Console

## Symptom

Windows Server lock screen displayed "Press Ctrl+Alt+Del to unlock," but pressing the physical key combination did nothing.

## Diagnosis

The keystroke was being intercepted before it ever reached the VM.

## Root Cause

The host operating system captures the real Ctrl+Alt+Del combination itself (it's a reserved OS-level secure-attention sequence), so it never gets forwarded through the browser to the noVNC console session inside Proxmox.

## Fix

Used the noVNC console's built-in toolbar option (labeled "Ctrl+Alt+Del" in the top toolbar dropdown), which sends the key sequence directly to the VM rather than relying on the physical keyboard shortcut.

## Takeaway

Any Proxmox noVNC console session needs the in-browser Ctrl+Alt+Del button for this exact reason — the physical shortcut will never reach a VM console over noVNC.
