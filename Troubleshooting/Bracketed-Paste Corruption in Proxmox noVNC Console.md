Bracketed-Paste Corruption in Proxmox noVNC Console

## Symptom

Pasting a command into the Proxmox web console (noVNC) produced literal garbage instead of running the command, e.g.:

```
root@proxmox:~# ^[[200~bash -c "$(wget -qLO - https://.../pihole.sh)"~
-bash: $'\E[200~bash': command not found
```

## Diagnosis

The `^[[200~` prefix and trailing `~` are standard bracketed-paste mode escape sequences — the terminal signaling "a paste just happened" so the shell can handle it specially. The noVNC console wasn't stripping these codes before they reached bash, so they showed up as literal input instead of being interpreted.

## Root Cause

A rendering/input-handling quirk specific to Proxmox's browser-based noVNC console; not something wrong with the command itself or the SSH-equivalent terminal.

## Fix

A few workable options, in order of reliability:

1. Disable bracketed paste for the session before pasting: `bind 'set enable-bracketed-paste off'`
2. Retype the command manually instead of pasting (fine for short one-liners)
3. Restructure a `bash -c "$(wget ...)"` command as a plain pipe instead — `wget -qLO - <url> | bash` — which is less prone to the same corruption
4. Best long-term fix: avoid the noVNC console entirely for anything beyond emergency access, and use a real SSH client (see [[SSH over Proxmox Console for Linux VM Management]] for the same reasoning applied to VMs — the same logic applies to LXC containers and the Proxmox host shell itself)

## Takeaway

The noVNC browser console is a reasonable fallback when SSH isn't reachable, but it has real input-handling limitations (this, and separately, text-selection freezing — see [[QuickEdit Mode and noVNC Selection Freezing Console Input]]). Prefer a real terminal/SSH client for anything interactive.
