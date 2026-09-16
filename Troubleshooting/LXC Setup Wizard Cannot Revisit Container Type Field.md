LXC Setup Wizard Cannot Revisit Container Type Field

## Symptom

Partway through the Zammad Community Script's Advanced Settings wizard, the Confirm Settings screen showed Container Type as Unprivileged (the default) instead of Privileged as planned. Attempting to navigate back through the wizard to change it wasn't possible.

## Diagnosis

The script's whiptail-style TUI wizard doesn't support jumping back to an arbitrary earlier field once past it — "back" either wasn't available or dropped further than a single step.

## Root Cause

Wizard design limitation: Container Type has to be caught and set correctly the first time it's presented, since the flow doesn't reliably support revisiting just that one field later.

## Fix

Proceeded with the build as Unprivileged on the first attempt rather than restarting the whole wizard. This build later turned out to have failed for an unrelated reason (see [Interrupted Zammad Install Leaves Container Running With No Services](Interrupted%20Zammad%20Install%20Leaves%20Container%20Running%20With%20No%20Services.md)) and was destroyed and rebuilt from scratch, this time selecting Privileged correctly on the first pass through Advanced Settings.

## Takeaway

Watch for the Container Type prompt closely the moment Advanced Settings starts in this script — don't assume it can be corrected later in the same run.
