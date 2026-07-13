Claude Desktop - Filesystem MCP over Code Tab

## Decision

Used the free Filesystem MCP Server in regular Claude Desktop chat to give Claude direct read/write access to the Homelab vault, instead of using Claude Code's Code tab.

## Context

Wanted Claude to be able to read and write documentation directly into the Obsidian vault, rather than manually copy-pasting content back and forth every session.

## Reasoning

Claude Code (the Code tab in Claude Desktop) requires a paid plan (Pro, Max, Team, or Enterprise). Not intending to upgrade from the free plan. The Filesystem MCP Server achieves the same practical outcome — direct file read/write in a specified folder — through regular free-tier chat instead.

## Setup Details

- Installed Node.js to support running the MCP server via `npx`
- Configured `claude_desktop_config.json` (Settings → Developer → Edit Config) to point the filesystem server at `C:\Obsidian\HomeLab`
- Confirmed working via Settings → Developer → Local MCP servers showing "running," and by having Claude list the vault's actual folder structure

## Tradeoff Accepted

No visual diff review before file edits, and slightly more manual initial setup (editing a JSON config file rather than clicking "select folder") compared to Claude Code. Day-to-day conversational use is otherwise the same.
