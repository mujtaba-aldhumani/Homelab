Single IT Support Group over Per-Department Groups for Zammad

## Decision

Configured Zammad with a single "IT Support" group handling all tickets, rather than mirroring the Active Directory Sales/IT/HR departments as three separate routing groups. Department is instead captured as metadata via Zammad Organizations (Sales, IT, HR), assigned to each customer's account.

## Context

The Active Directory project's OU/group structure models three business departments (Sales, IT, HR). The initial instinct was to mirror that same three-way split as Zammad Groups. Zammad's "Group" concept, however, represents who handles a ticket (a support queue), not who the ticket is about.

## Reasoning

A per-department Groups structure would only make sense if each department had its own embedded support staff — a model that doesn't match a small company with one central IT/helpdesk team. Using a single "IT Support" group, with the requester's department captured via the Organization field instead, mirrors how most real companies actually run a helpdesk: one central queue serving every department. This also reuses the same fictional employees (Sam Patel, Alex Chen, Jordan Lee) built for the AD project, tying the two projects together narratively rather than inventing a new department model just for Zammad.

## Status

Adopted.

## Related

- [Zammad](../Projects/Zammad.md)
- [Active Directory](../Projects/Active%20Directory.md)
