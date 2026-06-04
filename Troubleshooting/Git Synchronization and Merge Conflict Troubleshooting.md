## Issue

Changes made on the Lenovo ThinkCentre M720q were not appearing on the ASUS laptop.

Subsequent synchronization attempts resulted in merge conflicts.

## Environment

### Device 1

- Lenovo ThinkCentre M720q
- Windows 11 Pro
- Obsidian
- GitHub Desktop

### Device 2

- ASUS Laptop
- Obsidian
- GitHub Desktop

## Root Cause Analysis

### Initial Issue

The laptop had not pulled the most recent changes from GitHub after updates were committed from the Lenovo system.

### Secondary Issue

A merge conflict occurred within:

.obsidian/plugins/obsidian-git/data.json

This file stores Obsidian Git plugin configuration settings.

Both devices modified the file independently, causing Git to require manual conflict resolution.

## Resolution Steps

### Step 1

Verified that project documentation had successfully synchronized to GitHub.

### Step 2

Confirmed that the conflict existed only within Obsidian configuration files.

### Step 3

Aborted the failed merge process.

### Step 4

Removed conflicting plugin configuration data.

### Step 5

Re-synchronized repository contents.

### Step 6

Verified successful synchronization across both devices.

## Validation

Successfully verified:

Lenovo → GitHub → ASUS Laptop

and

ASUS Laptop → GitHub → Lenovo

synchronization workflows.

## Lessons Learned

- Git conflicts are not always related to project files.
- Obsidian configuration files are frequent sources of merge conflicts.
- Verifying GitHub as the source of truth simplifies troubleshooting.
- Synchronization should be tested before beginning major infrastructure work.

## Future Improvements

Consider excluding machine-specific configuration files from version control to reduce future synchronization conflicts.