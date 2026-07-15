pct exec Not Resolving Binary via PATH

## Symptom

After Pi-hole's install completed successfully, both of these failed:

```
pct exec 102 -- which pihole
pct exec 102 -- pihole setpassword
```

with:

```
lxc-attach: 102: ../src/lxc/attach.c: lxc_attach_run_command: 1845 No such file or directory - Failed to exec "pihole"
```

## Diagnosis

Confirmed the container ID was correct (`pct list` showed 102, running). Checked whether the binary actually existed rather than assuming the install had silently failed:

```
pct exec 102 -- ls -la /usr/local/bin/ | grep -i pihole
```

This returned the file, correctly present and executable (`-rwxr-xr-x ... pihole`).

## Root Cause

`pct exec` runs commands in a minimal environment that doesn't always load the container's full shell `PATH`, so a bare command name (`pihole`) isn't found even though the file exists and is executable at a standard location.

## Fix

Call the binary by its full absolute path instead of relying on `PATH` resolution:

```
pct exec 102 -- /usr/local/bin/pihole setpassword
```

This worked immediately.

## Takeaway

If `pct exec` can't find a command that's confirmed to exist and be executable, suspect a `PATH` issue in the exec environment before suspecting the install itself — verify the file's presence directly (`ls`) and fall back to its full path rather than the bare command name.
