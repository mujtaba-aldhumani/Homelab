GUI over PowerShell for Account-Lifecycle Practice

## Decision

Practiced the four common account-lifecycle helpdesk tasks (disable, password reset, unlock, move between OUs) using Active Directory Users and Computers, rather than continuing with PowerShell cmdlets for these specific tasks.

## Context

The bulk user provisioning step immediately before this used a PowerShell script (`New-ADUser` over a CSV loop). The natural next step was practicing routine lifecycle operations against those accounts, and either interface — PowerShell or the ADUC GUI — could accomplish all four.

## Reasoning

Bulk provisioning already proved the scripting/automation skill. These four tasks are a different category — one-off, single-account actions — and are genuinely how a lot of entry-level helpdesk work happens day to day: a ticket says "reset this one user's password," and the resolution is a few GUI clicks, not a script. Doing them in ADUC also builds knowledge of *where* these controls live in the interface (which tab holds lockout status, where the "must change password" checkbox is), which is separate, useful knowledge from knowing the equivalent cmdlet. Doing both — scripted bulk creation and GUI-based lifecycle management — gives a stronger overall answer than leaning on one interface for everything.

## Status

Adopted.

## Related

- [Active Directory](../Projects/Active%20Directory.md)
