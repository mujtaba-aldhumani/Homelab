Zammad Owner Dropdown Missing Newly-Created Agent

## Symptom

A test ticket ("Password Reset," internal ID 2 / ticket number 10002) submitted by customer Alex Chen against the `IT Support` group didn't offer the newly-created agent Taylor Morgan as an Owner option — only the existing admin account appeared in the dropdown, despite Taylor's account having the Agent role and Read/Create/Change permission on the `IT Support` group.

## Diagnosis

Confirmed via the Zammad Rails console (`zammad run rails runner`, run through `pct exec 104`) rather than the UI, to rule out UI display confusion:

- `Ticket.find(2).group.name` confirmed the ticket was genuinely in the `IT Support` group (note: the ticket's database `id` is 2; `10002` is only the display Ticket Number — these are different fields and easy to confuse when querying directly)
- `User.find_by(email: 'taylor.morgan@mujtaba.internal').user_groups.each { |ug| puts ug.attributes.inspect }` confirmed three separate `UserGroup` rows for the same group (`group_id: 1`), one each for `read`, `create`, and `change` access — Zammad stores each access level as its own row rather than a list on one row, so this was correct data, not duplication
- Confirmed Taylor's account was active with the Agent role

Also noticed in passing while investigating: two groups existed in Zammad (`IT` and `IT Support`) — a separate leftover-group issue, resolved independently (see [Duplicate IT Group Left Over From Initial Zammad Setup](Duplicate%20IT%20Group%20Left%20Over%20From%20Initial%20Zammad%20Setup.md)) and not the cause of this issue.

## Root Cause

Zammad requires a user to hold the group's **Full** access level — not just Read/Create/Change — before that user is eligible to be set as a ticket's Owner in that group. Read/Create/Change is enough to see and work tickets in a group, but Owner-assignment eligibility specifically checks for Full.

## Fix

Granted "Full" access on the `IT Support` group to Taylor's account, confirmed via further trial and error, after which Taylor appeared correctly as an assignable Owner.

## Takeaway

When setting up a new Zammad agent, grant **Full** access on their working group from the start if they'll ever need to own tickets in it — Read/Create/Change alone is sufficient for an agent to see and act on a group's tickets, but not to be assigned as Owner.
