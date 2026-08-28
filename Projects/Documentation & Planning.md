## Goal

Build the documentation and portfolio infrastructure behind the homelab itself — the vault, its sync setup, Claude's direct access to it — and establish a deliberate approach for choosing which projects to build and why.

## Vault and Sync Setup

- Installed Obsidian, GitHub Desktop, Git for Windows, and the Obsidian Git plugin
- Created the Homelab vault, its GitHub repository, and the initial folder structure
- Configured multi-device sync between the Lenovo ThinkCentre M720q (server) and an ASUS laptop (management workstation), both routed through GitHub
- Verified sync working in all directions: Lenovo → GitHub, GitHub → ASUS, ASUS → GitHub

### Troubleshooting

- Changes made on one device weren't appearing on the other, and a subsequent sync attempt produced a merge conflict inside Obsidian's own plugin config file (`.obsidian/plugins/obsidian-git/data.json`) — resolved by aborting the merge, clearing the conflicting plugin config, and re-syncing — [Git Synchronization and Merge Conflict Troubleshooting](../Troubleshooting/Git%20Synchronization%20and%20Merge%20Conflict%20Troubleshooting.md)

## Claude Desktop Integration

Set up direct file access between Claude and the vault, so documentation can be read and written directly instead of manual copy-paste.

- Since staying on the free Claude plan (Claude Code's Code tab requires a paid plan), used the free **Filesystem MCP Server** instead, which gives regular Claude Desktop chat direct read/write access to a specified folder — see [Claude Desktop - Filesystem MCP over Code Tab](../Decisions/Claude%20Desktop%20-%20Filesystem%20MCP%20over%20Code%20Tab.md)
- Installed Node.js to support running the MCP server via `npx`; configured `claude_desktop_config.json` to point the filesystem server at `C:\Obsidian\HomeLab`
- Verified working end-to-end: the server shows as running in Settings → Developer → Local MCP servers, and Claude can list the vault's folder structure and read/write files directly

## Vault Scope Decision

Decided to keep this Homelab vault separate from any future personal "Life" vault (a sibling folder, never merged), since this vault is intended to double as a portfolio piece for employers — see [Vault Separation - Homelab vs Life](../Decisions/Vault%20Separation%20-%20Homelab%20vs%20Life.md).

## Project Selection Methodology

Before resuming hands-on work partway through the homelab, stepped back to think through how projects should be chosen going forward, rather than picking randomly or following a single fixed roadmap.

- Clarified the underlying purpose split: a couple of pieces (Stremio + Real-Debrid, Pi-hole) are genuine personal use; everything else exists primarily to build demonstrable experience for landing a job or internship, since there's currently no real tech experience to point to
- No final career direction decided — leaning toward cloud and networking as an industry direction, but wanting breadth before narrowing
- Treated the original project list (initially suggested by ChatGPT) as an idea bank, not a fixed roadmap
- Wanted project selection to deliberately rotate across fields — helpdesk, sysadmin, networking, cybersecurity, cloud — to sample what actually fits, rather than staying in one lane
- Wanted a reflection step after each project: what was liked/disliked, what came easily vs. felt like a struggle, to narrow down a career direction from real experience rather than guessing upfront
- Researched real employer expectations (job postings, r/ITCareerQuestions, r/sysadmin, r/cybersecurity) to ground project ideas in what's actually asked for, rather than a generic list:
  - **Helpdesk** — ticketing systems (ServiceNow/Zendesk/Jira), remote support tooling, cross-platform OS familiarity, documentation habits
  - **Sysadmin** — breadth across tools/OSes valued over depth in one; used the public `trimstray/test-your-sysadmin-skills` checklist as a self-assessment reference
  - **Cybersecurity** — worked backwards from job postings for recurring themes: SIEM/log analysis, brute-force login detection, basic GRC documentation, OWASP-style scanning
  - **Networking** — TCP/IP, VLANs, routing/switching, firewall/VPN concepts, with CCNA/Network+ as the credential signal; postings explicitly accept homelab/school projects as proof of hands-on skill
- Proposed a tentative first rotation (not committed to as a strict plan): helpdesk ticketing system → sysadmin/Active Directory → networking/pfSense+VLANs → cybersecurity/SIEM → cloud mirror of an existing lab service

## Status

Vault, sync, and Claude integration all set up and working. Project rotation approach (helpdesk / sysadmin / networking / cybersecurity / cloud, one per field, with reflection after each) adopted as the guiding method for choosing what to build next — treated as a loose approach, not a locked roadmap.

## Related Decisions

- [Claude Desktop - Filesystem MCP over Code Tab](../Decisions/Claude%20Desktop%20-%20Filesystem%20MCP%20over%20Code%20Tab.md)
- [Vault Separation - Homelab vs Life](../Decisions/Vault%20Separation%20-%20Homelab%20vs%20Life.md)

## Project Log

### 2026-06-03

- Received the Lenovo ThinkCentre M720q; set up Obsidian, GitHub, and multi-device sync; resolved a Git merge conflict; established the initial project roadmap (Proxmox → Ubuntu → Tailscale → Docker → AD → Monitoring → Wazuh) and device architecture (ASUS = workstation, Lenovo = server)

### 2026-07-12

- Set up Claude Desktop's filesystem MCP integration; discussed and decided on vault separation from any future personal vault

### 2026-07-13

- Worked through the project-selection methodology above and researched real employer expectations across five IT fields — see [Daily Log — 2026-07-13](../Daily%20Logs/2026-07-13.md) for the full research notes

## Next Steps

1. Continue using the rotation approach for future project selection, given about a month left until the career fair
