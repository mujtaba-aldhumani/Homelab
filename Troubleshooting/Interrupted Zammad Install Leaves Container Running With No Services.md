Interrupted Zammad Install Leaves Container Running With No Services

## Issue

The Zammad Community Script reported a completed install (site described as "up and running"), but the web UI returned "Unable to connect" in the browser, and console login to the container also failed.

## Environment

Proxmox host, LXC container 104 (Unprivileged, first build attempt), Debian 12 (bookworm) base image, static IP `192.168.86.204/24`.

## Root Cause Analysis

Checked in order, from the Proxmox host:

- `pct status 104` / `pct config 104` — container running, correct static IP and network config. Rules out a networking/firewall problem.
- `pct exec 104 -- systemctl status nginx elasticsearch zammad-web` — all three returned "Unit could not be found," not "failed" or "inactive." This is a stronger signal than a crashed service: it means these services were never installed at all.
- `ls -la /var/log | grep -i install` and `ls -la /tmp | grep -i install` — no install log found anywhere in the container.
- `ls -la /opt`, `ls -la /root`, `cat /etc/os-release`, `which curl wget git` — `/opt` empty, base OS confirmed as Debian 12 correctly, but only `wget` was present; `curl` and `git` (both needed early by the install script to fetch dependencies) were missing.
- `journalctl -u pve-container@104` on the host — showed the container starting, deactivating ~8 seconds later, then starting again shortly after — a short, unusual lifecycle right around the time the script would have been moving from container creation into the in-container application install phase.

Conclusion: the script's container-creation phase completed fully and correctly (networking, SSH key, hostname all set up right), but the second phase — attaching into the container to actually install PostgreSQL, Redis, Elasticsearch, nginx, and the Zammad app — never ran. The leading theory is that switching to a second Proxmox shell session mid-script (to separately fix the container's root password for console access) interrupted or disconnected the original script's session before it reached the install phase, even though the script had already reported success for the portion it did complete.

## Resolution Steps

1. `pct stop 104`
2. `pct destroy 104`
3. Re-ran the script from scratch, staying in a single, uninterrupted shell session the entire way through — including past container creation and into the actual application install — this time correctly selecting Privileged Container Type in Advanced Settings.

## Validation

Post-rebuild: `pct exec 104 -- systemctl status elasticsearch` showed `active (running)` with real uptime; `zammad-web`, `zammad-worker`, and `zammad-websocket` all confirmed running with real backing processes via `ss -tlnp` and `ps aux` (Puma on port 3000, a ruby process on 6042, nginx on 80). The web UI loaded successfully.

## Lessons Learned

A community script reporting "done" only reflects the steps it actually executed — it's not proof every dependent service is healthy. When a multi-phase installer (container creation, then an in-container application install) needs to run start-to-finish, don't switch tasks or open a second session partway through; verify with `which`/`systemctl`/`ls` rather than trusting the script's own final message.

## Future Improvements

None planned — resolved by rebuilding cleanly. Verifying `curl`/`git` presence early (or checking for an install log) would have caught this failure sooner next time.
