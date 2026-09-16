Dedicated Helpdesk OU over Nesting Under Departments

## Decision

Created a new top-level `Helpdesk` OU for the Taylor Morgan helpdesk-tech account, as a sibling to `Departments` rather than a child OU nested inside it.

## Context

`Departments` (containing `Sales`, `IT`, `HR`) represents the fictional company's business units — regular employees who generate support tickets. Taylor Morgan is IT support staff, a functional role serving all three departments, not a member of any one of them.

## Reasoning

Nesting Helpdesk inside Departments would visually and structurally conflate "IT the business department" with "IT support the function," which aren't the same thing in this model — the same reasoning already applied when choosing a single central Zammad group over per-department queues (see [Single IT Support Group over Per-Department Groups for Zammad](Single%20IT%20Support%20Group%20over%20Per-Department%20Groups%20for%20Zammad.md)). There's also a practical AD reason: keeping delegated/elevated-access accounts in their own OU, separate from standard employee OUs, makes auditing who holds special permissions straightforward, and prevents future GPOs or cleanup processes aimed at regular department employees from accidentally reaching privileged support accounts.

## Status

Adopted. The `Helpdesk-Techs` security group's delegated permission still targets the `Departments` OU (granting rights over the users inside it) — that delegation works regardless of where Taylor's own account physically sits in the OU tree.

## Related

- [Active Directory](../Projects/Active%20Directory.md)
