Narrow Password-Reset-Only Delegation for Helpdesk-Techs

## Decision

Delegated only "Reset user passwords and force password change at next logon" to the `Helpdesk-Techs` security group on the `Departments` OU, rather than the broader common-task set (create/delete/manage user accounts, create/delete/manage groups) initially selected in the Delegation of Control Wizard.

## Context

While extending the Active Directory project with a fictional helpdesk technician (Taylor Morgan) to work Zammad tickets, the Delegation of Control Wizard's "common tasks" list was used to grant AD permissions. The first pass through the wizard checked three boxes: "Create, delete, and manage user accounts," "Reset user passwords and force password change at next logon," and "Create, delete and manage groups."

## Reasoning

The entire point of the exercise was demonstrating least privilege — a helpdesk tech resolving password-reset tickets has no legitimate need to create or delete employee accounts or manage security groups; those are HR/onboarding or senior-IT functions. Granting all three would have given Taylor admin-equivalent power over the OU, defeating the "helpdesk tech, not domain admin" scenario. Narrowing to just the password-reset task keeps the delegation meaningfully scoped, and made the later negative-permission test (confirming Taylor could *not* delete an account or add one to a privileged group) actually mean something.

Microsoft's modern password-reset common task also bundles account-unlock permission (write access to `pwdLastSet` and `lockoutTime`) into the same checkbox, so no second task was needed to also cover unlocking accounts.

## Status

Adopted. Verified: as Taylor, resetting Alex Chen's password succeeded; attempting to delete Alex's account or add Alex to a privileged group was denied.

## Related

- [Active Directory](../Projects/Active%20Directory.md)
- [Zammad](../Projects/Zammad.md)
