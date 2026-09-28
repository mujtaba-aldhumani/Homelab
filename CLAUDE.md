# Read Me First

This file documents how this vault is organized and formatted. Read this at the start of every session before writing or editing anything in the vault — it captures conventions established across multiple sessions, not just guesses.

This vault doubles as a portfolio piece for employers, so consistency matters more here than in a private notebook.

## Folder Structure

- **Daily Logs/** — one file per session, named `YYYY-MM-DD.md`. A running record of what actually happened.
- **Decisions/** — one file per meaningful decision, named descriptively as `X over Y for Z` or `X over Y` (the choice made, not a topic name). Captures *why* a choice was made, not just what was chosen.
- **Projects/** — one file per project/system (e.g. `Proxmox.md`). The current-state summary and index for that project — what exists, its status, and links out to the Decisions/Troubleshooting/Daily Logs that explain how it got there.
- **Troubleshooting/** — one file per distinct problem encountered, named descriptively after the symptom/cause. Full diagnostic detail lives here, not in the daily log.
- **Screenshots/** — supporting images, embedded via standard markdown image syntax with a relative path and an italicized caption underneath.

## The Core Rule: Detail Lives in One Place, Not Three

Don't duplicate full troubleshooting or decision detail across multiple files. The pattern:

- **Troubleshooting folder** and **Decisions folder** are the source of truth — full detail goes there.
- **Daily Log** gets a one-line summary per issue/decision, plus a link out. Not the full Symptom/Diagnosis/Root Cause writeup inline.
- **Project file** gets a short status-level mention plus a link, in both the relevant system section and the dated Project Log.

If you're about to write more than 2-3 sentences about a troubleshooting issue or a decision's reasoning *inside a daily log or project file*, stop — that detail belongs in its own Troubleshooting/Decisions file instead, with just a link left behind.

## Linking — Critical

**Never use Obsidian wiki-links (`[[Note Name]]`).** They render fine in Obsidian but show up as literal bracketed text on GitHub, and this vault needs to work in both places.

Always use standard markdown relative links instead, with spaces in filenames URL-encoded as `%20`:

```
[Display Text](../Folder%20Name/File%20Name.md)
```

- From a file in `Daily Logs/` linking to `Decisions/`: `../Decisions/File%20Name.md`
- From a file in `Decisions/` linking to another file in the same `Decisions/` folder: `File%20Name.md` (no `../` needed, same folder)
- From a file in `Projects/` linking to `Troubleshooting/`: `../Troubleshooting/File%20Name.md`
- Same pattern for `Daily Logs/`, always with the space in "Daily Logs" encoded: `../Daily%20Logs/2026-07-14.md`

When creating a new file that will be linked from elsewhere, or editing an existing file's links, double check the relative path actually matches the two files' real folder locations — don't assume.

## Daily Logs — Format

```
## Work Completed

### <Project/Topic Name>

<1-2 sentence goal/context>

* <what was done, decisions made inline as one-liners with a link to the Decisions file for full reasoning>

#### Troubleshooting

<Only if issues came up. One line per issue + link to its Troubleshooting file. Do not restate the full diagnosis here.>

* <symptom in one line> — [Descriptive Title](../Troubleshooting/Descriptive%20Title.md)

### Result / other subsections as needed

## Status

<Current state in a few bullets>

## Next Steps

1. <numbered list>
```

After creating a new daily log, **add it to the top of `Daily Logs/README.md`** (newest-first index — see below). This doesn't happen automatically; do it in the same turn.

### Why Daily Logs/README.md exists

GitHub's folder browser always sorts files alphabetically, which means oldest-first for `YYYY-MM-DD` filenames, and there's no setting to reverse it. `Daily Logs/README.md` is a manually-maintained newest-first index that GitHub automatically renders below the file listing when browsing that folder. Keep it current — add each new log to the top of the list when it's created.

## Decisions — Format

Filename and first line both match the decision itself (not a topic label): `X over Y for Z.md`.

```
X over Y for Z

## Decision

<One or two sentences: what was chosen over what, for what purpose.>

## Context

<The situation/requirement that prompted the choice.>

## Reasoning

<Why this option won over the alternative(s). This is the part worth being thorough in.>

## Alternatives Considered

<Optional — only if there's a genuine alternative worth naming that isn't already covered in Reasoning.>

## Status

<Adopted / Superseded (link to what superseded it) / Deferred, etc.>

## Related

- [Project Name](../Projects/Project%20Name.md)
```

## Troubleshooting — Format

Two templates are in use — pick based on scope, don't force one onto the other:

**Short form** (a single, contained issue):
```
Descriptive Title

## Symptom

## Diagnosis

## Root Cause

## Fix

## Takeaway
```

**Long form** (multi-step, environment-heavy issues — e.g. an OS install with multiple sub-problems):
```
Descriptive Title

## Issue

## Environment

## Root Cause Analysis

## Resolution Steps

## Validation

## Lessons Learned

## Future Improvements
```

## Projects — Format

```
## Goal

## Hardware / Planned Skills (if applicable)

## Virtual Machines / LXC Containers

### <Name> (<hostname> / VMID <n>)

Purpose: ...
Status: ...
Specs: ...

![Caption](../Screenshots/file.png)
*Italicized caption.*

## Status

<Current overall state, a paragraph.>

## Related Decisions

- [Decision Title](../Decisions/Decision%20Title.md)

## Project Log

### YYYY-MM-DD

- <what happened, linking out to Decisions/Troubleshooting/Daily Logs as needed>

## Next Steps

1. <numbered list>
```

## Teaching Conventions

The owner is learning, and wants to be able to explain every project to employers. Apply these to every project, in the docs and in live guidance:

1. **Security alongside the main lesson.** He wants to end up in cybersecurity, so every project teaches at least one security principle or technique (for example least privilege, default deny, attack surface reduction, defense in depth) tied to what is actually being built. Each project file gets a short "Security Takeaways" section.
2. **Acronyms explained on first use**, with a small, simple explanation of each term, e.g. "VLAN (Virtual Local Area Network) — a way to split one network into separate, isolated groups."
3. **Introduce terms gradually.** Don't stack many new technical terms in one paragraph; explain each before building on it.

## End-of-Session Checklist

Before ending any session that touched the lab or the vault, walk this list. Documentation is Claude's job every session, not a follow-up the user should have to request.

1. **Daily Log** — create `YYYY-MM-DD.md` for the session and add it to the top of `Daily Logs/README.md`.
2. **Project file(s)** — update the affected file's status, Project Log, and Next Steps. If a guest VM/LXC was added, removed, or repurposed, update the table and counts in `Projects/Proxmox.md` and the affected guest's own project file.
3. **Decisions / Troubleshooting** — one file per decision or issue, and add each new file to its folder's `README.md` under the right project heading.
4. **Root `README.md`** — keep the project table and architecture diagram in step with the lab: add new projects, and extend a row when a project is meaningfully extended.
5. **Scripts and configs** — anything a project's docs describe as written (scripts, config files) should be stored in the vault, not only described.
6. **Link check** — spot-check that new relative links resolve to real files.

## Vault Scope

This vault (Homelab) stays separate from any future personal "Life" vault — sibling folder, never merged — since this one is intended to double as a portfolio piece. Don't add personal/non-homelab content here.
