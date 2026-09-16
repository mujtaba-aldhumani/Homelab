Zammad Owner Dropdown Missing Newly-Created Agent

## Symptom

A test ticket ("Password Reset," internal ID 2 / ticket number 10002) submitted by customer Alex Chen against the `IT Support` group didn't offer the newly-created agent Taylor Morgan as an Owner option — only the existing admin account appeared in the dropdown, despite Taylor's account having the Agent role and Read/Create/Change permission on the `IT Support` group.

## Diagnosis

Confirmed via the Zammad Rails console (`zammad run rails runner`, run through `pct exec 104`) rather than the UI, to rule out UI display confusion:

- `Ticket.find(2).group.name` confirmed the ticket was genuinely in the `IT Support` group (note: the ticket's database `id` is 2; `10002` is only the display Ticket Number — these are different fields and easy to confuse when querying directly)
- `User.find_by(email: 'taylor.morgan@mujtaba.internal').user_groups.each { |ug| puts ug.attributes.inspect }` confirmed three separate `UserGroup` rows for the same group (`group_id: 1`), one each for `read`, `create`, and `change` access — Zammad stores each access level as its own row rather than a list on one row, so this was correct data, not duplication
- Confirmed Taylor's account was active with the Agent role

Also noticed in passing while investigating: two groups existed in Zammad (`IT` and `IT Support`) rather than the single intended group — likely a leftover default group never cleaned up. Flagged as a separate minor cleanup item, not confirmed as the cause of this issue.

## Root Cause

Not conclusively identified. Everything checked at the data level (group membership, access rows, active/Agent status, correct ticket group) looked correct for Taylor to be a valid Owner candidate. Granting the `IT Support` group's "Full" access level to Taylor (a broader permission tier than Read/Create/Change) resolved the issue and Taylor then appeared as an assignable Owner. It's possible Zammad requires "Full" group access specifically for ticket-ownership eligibility, or the fix coincided with a session relogin that cleared a stale frontend cache — both were plausible and neither was isolated definitively.

## Fix

Granted "Full" access on the `IT Support` group to Taylor's account, which resolved the missing-Owner-option issue.

## Takeaway

Left as an open question rather than a fully-understood root cause. If this recurs on a future agent account, try a clean logout/hard-refresh first (to rule out frontend caching) before escalating straight to a broader permission grant — and note that "Full" access may simply be a genuine Zammad requirement for ticket ownership, not just least-privilege overreach.
