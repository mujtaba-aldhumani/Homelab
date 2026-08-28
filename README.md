# Homelab

A personal homelab built for hands-on IT infrastructure practice — sysadmin, networking, and security fundamentals — documented as it's built, mistakes and all.

Everything here runs on a single physical box, virtualized with Proxmox. Each project below started from an actual need or learning goal, not a scripted tutorial, and the documentation reflects that: decisions made along the way, problems actually hit, and how they got fixed.

## Hardware

- Lenovo ThinkCentre M720q — Intel i5-8400T, 16GB RAM, 500GB SSD
- Proxmox VE 9.2 as the hypervisor

## Projects

| Project | What it does | Status |
|---|---|---|
| **Active Directory** | Single-domain AD forest (`mujtaba.internal`) — domain controller, OU/security group structure for a small fictional company, Group Policy enforcement verified against a real domain-joined client | Core build complete |
| **Pi-hole** | Network-wide DNS-based ad blocking, covering the whole home LAN and remote devices via Tailscale | Complete |
| **Tailscale** | Remote access to the whole home network (subnet router) plus an exit node for sharing the home IP with specific outside devices | Complete |

Full build detail, the reasoning behind specific choices, and every issue hit along the way live in this vault — see **How This Vault Is Organized** below.

## How This Vault Is Organized

This repo doubles as a running engineering journal, not just a project showcase. Four folders, each with one job:

- **[Projects/](Projects/)** — one file per system. Start here for the current state of anything: what exists, its status, and links out to the detail behind it.
- **[Daily Logs/](Daily%20Logs/)** — a dated log of what actually happened in each work session. See the [index](Daily%20Logs/README.md) for the most recent entries first.
- **[Decisions/](Decisions/)** — one file per meaningful choice, written as *what was chosen over what, and why* — not just a settings dump.
- **[Troubleshooting/](Troubleshooting/)** — one file per real problem hit, in Symptom → Diagnosis → Root Cause → Fix format. Nothing here is hypothetical; everything was actually encountered.

The goal of splitting it this way: a project file gives you the current picture in 30 seconds, while the Decisions and Troubleshooting folders let anyone curious dig into *why* something is built the way it is, without wading through unrelated project history to find it.

## About

Built by Mujtaba, a Management Information Systems student, as a portfolio project while exploring sysadmin, networking, and security as a career direction. More projects are added as time allows — check [Projects/](Projects/) for the current lineup.
