QuickEdit Mode and noVNC Selection Freezing Console Input

## Symptom

Multiple times during the Pi-hole install wizard, keypresses (Enter, arrow keys) stopped registering entirely — screens appeared "stuck" with no visible response to input, in both the Proxmox noVNC browser console and a Windows PowerShell SSH session.

## Diagnosis

Checked whether the underlying process was actually still alive rather than assuming a hang: found the wizard's `whiptail` confirmation dialog still running and waiting on its terminal device (`ps aux | grep whiptail` showed it active). This ruled out the script itself being frozen or crashed — the process was fine, but input wasn't reaching it.

Re-examined screenshots of the "stuck" screens and noticed large blue-highlighted blocks covering the terminal — consistent with an active text selection, not a rendering artifact.

## Root Cause

Two related but distinct causes of the same symptom:

1. **In the noVNC browser console:** an accidental click-drag selected text on the page, which can interfere with keystrokes reaching the embedded terminal.
2. **In Windows PowerShell/cmd:** QuickEdit Mode, enabled by default in Windows console windows, freezes all input to the running process the moment any text is selected (even accidentally, from a stray click) — it stays frozen until the selection is explicitly cleared, with no visible error or indication that this is what's happening.

## Fix

- Click once (a clean click, not a drag) inside the terminal to clear the selection, then retry the keypress.
- Permanent fix for PowerShell/cmd going forward: right-click the window's title bar → **Properties** → **Options** tab → uncheck **QuickEdit Mode**.
- For the noVNC console specifically, avoid clicking/dragging inside the console area at all while a script is running — keyboard-only input is safer there.

## Takeaway

"Nothing is happening" after a keypress doesn't necessarily mean a hung process — check with `ps aux` (or equivalent) whether the process is actually still alive and waiting, before assuming it's broken. Windows console QuickEdit Mode is a common, easy-to-miss cause of console input silently not registering, and is worth disabling on any machine used regularly for SSH/console work.
