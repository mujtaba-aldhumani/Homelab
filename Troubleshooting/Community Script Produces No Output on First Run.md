Community Script Produces No Output on First Run

## Symptom

Ran the Zammad Proxmox Community Scripts one-liner (`bash -c "$(curl -fsSL .../zammad.sh)"`) in the Proxmox host shell. Nothing appeared to happen — no output, no wizard, no error.

## Diagnosis

Tested whether the host could actually reach the script URL at all, independent of the full one-liner, with `curl -fsSL <script-url> | head -20` to confirm outbound connectivity/DNS resolution from the Proxmox host and that the fetch itself worked.

## Root Cause

Not conclusively isolated, but the leading suspect is a corrupted paste of the one-liner into the shell — the command relies on nested `bash -c "..."` and command substitution, which silently breaks if quote characters get mangled (e.g. smart/curly quotes from a copy source) or if line breaks get introduced mid-command.

## Fix

Re-entered the command carefully; it ran successfully on retry.

## Takeaway

For multi-quote, single-line installer commands pasted into a console, a silent no-op is more likely to be a mangled paste than a network problem — verify the underlying `curl` fetch works on its own before assuming the host has connectivity issues.
