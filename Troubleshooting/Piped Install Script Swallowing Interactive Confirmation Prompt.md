Piped Install Script Swallowing Interactive Confirmation Prompt

## Symptom

Running the Pi-hole community install script as:

```
wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/pihole.sh | bash
```

built the LXC container successfully every time, but silently aborted right at Pi-hole's own "this will run a third-party installer" confirmation prompt (`Do you want to continue? [y/N]:`), regardless of what was typed or how carefully. The container would get auto-deleted a few seconds later with no real error, just "Aborted by user."

## Diagnosis

Watched the exact moment of failure closely across repeated attempts — the prompt would print, then immediately resolve to "no" with no visible keypress from the user actually landing. Ruled out terminal/keyboard focus issues first (had already separately dealt with those in the noVNC console and QuickEdit Mode), since this happened even in a clean, focused terminal.

## Root Cause

The command pipes the downloaded script directly into `bash`'s stdin (`| bash`). That same stdin stream is what `bash` uses to read input for any interactive prompt inside the script. Once the script's own text has been fully piped in, that stdin stream is effectively exhausted/at EOF — so when the Pi-hole installer later tries to read a "y/n" answer, there's nothing left to read, and it defaults to no/abort instantly. The keyboard was never connected to that prompt at all, no matter how deliberately it was answered.

## Fix

Download the script to a real file first, then execute that file as a separate command, so stdin stays connected to the actual keyboard/terminal:

```
wget -qL https://github.com/community-scripts/ProxmoxVE/raw/main/ct/pihole.sh -O pihole.sh
bash pihole.sh
```

Same script, same behavior otherwise — but any interactive prompt inside it (including Pi-hole's own installer disclaimer) now actually waits for and receives real input.

## Takeaway

`curl|bash` / `wget -qLO - ... | bash` one-liners are convenient but break any interactive prompt inside the piped script, since piping consumes stdin. If a script might prompt for input, download-then-run instead of pipe-then-run.
