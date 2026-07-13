Vault Separation - Homelab vs Life

## Decision

Keep the Homelab vault separate from any future personal/life vault, rather than merging everything into one Obsidian vault.

## Context

Interested in eventually building a broader "second brain" system covering personal projects, daily life, and other areas beyond the homelab — the kind of all-in-one setup often shown in Claude Code + Obsidian tutorials.

## Reasoning

The Homelab vault is intended to double as a portfolio piece to show employers. Mixing in personal content (daily life notes, unrelated projects) would work against that goal — a hiring manager browsing the GitHub repo shouldn't run into personal journal entries next to Proxmox documentation.

## Approach

If a "Life" vault is created later, it will live as a sibling folder (not nested inside or merged with Homelab). A Claude session pointed at the shared parent folder can still pull context from both vaults when needed (e.g. "how did homelab work affect my week"), without permanently mixing the two.

## Tradeoff Accepted

Notes in separate vaults can't use Obsidian's native `[[wikilink]]` backlinking across vaults — only within a single vault. This was not considered an important loss, since there isn't much need for direct interlinking between homelab notes and personal life notes.
