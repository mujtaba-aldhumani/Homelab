Proxmox Host Upgrade Fails on Enterprise Repository Without Subscription

## Symptom

Mid-script, the Zammad Community Script offered to run a host-level `apt update && apt upgrade` before proceeding. Choosing to run it returned `apt-get update exited with code 100 — some repositories may have failed`, with a hint pointing at an auth error on the Proxmox enterprise repository.

## Diagnosis

The script itself flagged the specific cause rather than requiring further digging: the failure was scoped only to the `pve-enterprise` repository, not a general network/DNS problem.

## Root Cause

The Proxmox enterprise repository requires a paid subscription to actually pull from. This host runs the free/no-subscription install, which still has the enterprise repo configured by default, so it always errors out on `apt update` unless removed or switched to the no-subscription repo.

## Fix

None needed for the immediate task — the script correctly recognized the failure was scoped to one repo and continued the upgrade using the working no-subscription repos.

## Takeaway

Expected, benign behavior for a non-paying Proxmox installation, not a real problem — safe to ignore per-run. Permanently switching `/etc/apt/sources.list.d/pve-enterprise.list` to the no-subscription repo would stop the warning from recurring on future updates.
