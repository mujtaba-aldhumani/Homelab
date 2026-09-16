## Goal

Stand up a helpdesk/ticketing system as the next candidate project in the career-exploration rotation, chosen for its multi-service architecture and closer resemblance to a modern support org's tooling than a single-service alternative — see [Zammad over osTicket for Ticketing Project](../Decisions/Zammad%20over%20osTicket%20for%20Ticketing%20Project.md). Extended partway through into a cross-project exercise with [Active Directory](Active%20Directory.md), simulating a real helpdesk technician's end-to-end workflow: ticket intake, delegated AD access, and resolution.

## LXC Container

### Zammad (zammad / VMID 104)

Purpose: Zammad ticketing system — nginx, PostgreSQL, Redis, Elasticsearch, and the Zammad Rails app itself, deployed via Proxmox Community Scripts.

Status: Running, verified healthy end-to-end.

Specs: Privileged LXC (see [Privileged over Unprivileged LXC for Zammad](../Decisions/Privileged%20over%20Unprivileged%20LXC%20for%20Zammad.md)), Debian 12 (bookworm), static IP `192.168.86.204/24`, gateway `192.168.86.1`, bridge `vmbr0`, IPv6 disabled (consistent with the rest of the network), 8GB disk, 2 cores, 4096MB RAM, SSH access provisioned via the Proxmox host's existing root key.

## Installation

- Ran the Community Script in Advanced Settings mode to access the Container Type toggle, needed to select Privileged rather than the script's Unprivileged default
- First build attempt defaulted to Unprivileged when the wizard couldn't be navigated back to correct it — see [LXC Setup Wizard Cannot Revisit Container Type Field](../Troubleshooting/LXC%20Setup%20Wizard%20Cannot%20Revisit%20Container%20Type%20Field.md)
- Hit a silent no-output issue on the very first attempt to run the script at all — see [Community Script Produces No Output on First Run](../Troubleshooting/Community%20Script%20Produces%20No%20Output%20on%20First%20Run.md)
- Mid-install, the script offered a host-level package upgrade, which hit an expected, benign enterprise-repo auth error on this no-subscription Proxmox install — see [Proxmox Host Upgrade Fails on Enterprise Repository Without Subscription](../Troubleshooting/Proxmox%20Host%20Upgrade%20Fails%20on%20Enterprise%20Repository%20Without%20Subscription.md)
- The first build reported success but was actually non-functional — the in-container application install had silently never run, most likely due to a second shell session interrupting the script mid-way. Diagnosed via `systemctl`, `journalctl`, and checking for basic dependencies (`curl`/`git`) — see [Interrupted Zammad Install Leaves Container Running With No Services](../Troubleshooting/Interrupted%20Zammad%20Install%20Leaves%20Container%20Running%20With%20No%20Services.md)
- Destroyed and rebuilt the container from scratch in one uninterrupted session, correctly selecting Privileged this time — install completed successfully
- Post-rebuild, all four Zammad-related systemd units (`zammad`, `zammad-web`, `zammad-worker`, `zammad-websocket`) showed `/bin/sleep infinity` as their Main PID, which looked like a failure but was confirmed to be normal packaging behavior — see [Zammad systemd Units Show sleep infinity as Main Process](../Troubleshooting/Zammad%20systemd%20Units%20Show%20sleep%20infinity%20as%20Main%20Process.md)
- Verified Elasticsearch specifically (the known risk point) stable and running, and confirmed real Puma/nginx/websocket processes actually listening and serving traffic

## Group and Organization Structure

- Configured a single `IT Support` Zammad Group handling all tickets, rather than mirroring the AD project's three departments as separate routing groups — see [Single IT Support Group over Per-Department Groups for Zammad](../Decisions/Single%20IT%20Support%20Group%20over%20Per-Department%20Groups%20for%20Zammad.md)
- Created three Organizations (Sales, IT, HR) matching the AD department structure, and added Sam Patel, Alex Chen, and Jordan Lee as Customer-role users under their matching Organization
- Noted a leftover duplicate group (`IT` alongside `IT Support`) surfaced while troubleshooting agent permissions — not yet cleaned up, tracked in Next Steps

## Agent Setup and Active Directory Delegation

- Initially decided to work tickets from the existing personal admin account (adding the Agent role) rather than a separate persona — see [Own Admin Account over Separate Agent Persona for Zammad](../Decisions/Own%20Admin%20Account%20over%20Separate%20Agent%20Persona%20for%20Zammad.md)
- Revised that decision to more fully simulate a real job: created a distinct fictional agent, Taylor Morgan, as both a Zammad Agent (with `IT Support` group access) and a delegated Active Directory helpdesk-tech domain account — keeping one consistent identity across both systems. Full AD-side build (Helpdesk OU, `Helpdesk-Techs` security group, delegated permissions) documented in [Active Directory](Active%20Directory.md)
- While assigning a test ticket, Taylor didn't initially appear as a selectable ticket Owner despite correct role/group data — root cause not conclusively identified, resolved by granting Full group access — see [Zammad Owner Dropdown Missing Newly-Created Agent](../Troubleshooting/Zammad%20Owner%20Dropdown%20Missing%20Newly-Created%20Agent.md)

## End-to-End Ticket Workflow Test

Built to prove the two projects (Zammad + AD delegation) actually connect into one working simulation, not just two services that happen to exist side by side:

1. Logged in as customer Alex Chen and submitted a "Password Reset" ticket ("I forgot my password and I'm locked out of my computer, can someone reset it?") against `IT Support`
2. Logged in as agent Taylor Morgan, located the unassigned ticket via Overviews (new tickets don't appear under "My Assigned Tickets" until explicitly claimed), and assigned Owner to Taylor
3. Switched to VM100 and, using Taylor's delegated AD permission (password reset only, scoped to the `Departments` OU), actually reset Alex Chen's domain password
4. Verified the delegation's boundary held: as Taylor, attempting to delete Alex's account or add Alex to a privileged group was denied
5. Returned to Zammad, replied to Alex confirming the reset, and closed the ticket

## Status

Zammad running and verified healthy (all services confirmed via real process/port checks, not just `systemctl status`). Group/Organization structure, customer accounts, and an agent account built. Extended into a working cross-project simulation with Active Directory: a delegated, least-privilege helpdesk-tech identity that actually resolves tickets by performing the real AD action, with the permission boundary verified via a negative test. Full ticket lifecycle (submission → triage → AD-side fix → resolution) demonstrated end-to-end.

## Related Decisions

- [Zammad over osTicket for Ticketing Project](../Decisions/Zammad%20over%20osTicket%20for%20Ticketing%20Project.md)
- [Privileged over Unprivileged LXC for Zammad](../Decisions/Privileged%20over%20Unprivileged%20LXC%20for%20Zammad.md)
- [Own Admin Account over Separate Agent Persona for Zammad](../Decisions/Own%20Admin%20Account%20over%20Separate%20Agent%20Persona%20for%20Zammad.md)
- [Single IT Support Group over Per-Department Groups for Zammad](../Decisions/Single%20IT%20Support%20Group%20over%20Per-Department%20Groups%20for%20Zammad.md)
- [Narrow Password-Reset-Only Delegation for Helpdesk-Techs](../Decisions/Narrow%20Password-Reset-Only%20Delegation%20for%20Helpdesk-Techs.md)
- [Dedicated Helpdesk OU over Nesting Under Departments](../Decisions/Dedicated%20Helpdesk%20OU%20over%20Nesting%20Under%20Departments.md)

## Project Log

### 2026-09-14

- Decided Zammad over osTicket and Privileged LXC over Unprivileged
- Built the Zammad LXC (VMID 104), hit and resolved a silent script failure and an interrupted, non-functional first install; rebuilt cleanly and reached the working login page
- Full walkthrough and troubleshooting: [Daily Log — 2026-09-14](../Daily%20Logs/2026-09-14.md)

### 2026-09-15

- Built out Groups/Organizations, customer accounts, and an agent account; revised the agent-identity decision to create a dedicated Taylor Morgan persona shared with a new AD delegation build
- Extended Active Directory with a Helpdesk OU, `Helpdesk-Techs` group, and a narrowly-scoped password-reset delegation
- Ran a full end-to-end ticket workflow test, including a negative permission test proving the delegation's boundary holds
- Full walkthrough and troubleshooting: [Daily Log — 2026-09-15](../Daily%20Logs/2026-09-15.md)

## Next Steps

1. Clean up the leftover duplicate `IT` group in Zammad, leaving only `IT Support`
2. Revisit the Owner-dropdown permission requirement if it recurs, to pin down whether "Full" group access is a genuine Zammad requirement for ticket ownership
3. Decide the next homelab project from the candidate rotation — see [Documentation & Planning](Documentation%20&%20Planning.md)
