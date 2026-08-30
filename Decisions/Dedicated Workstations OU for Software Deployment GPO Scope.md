Dedicated Workstations OU for Software Deployment GPO Scope

## Decision

Created a new top-level `Workstations` OU and moved VM100's computer object into it, then scoped the software-deployment GPO to that OU specifically, rather than linking the GPO at the domain root or leaving the computer object in the default `Computers` container.

## Context

GPO Software Installation under Computer Configuration targets computer accounts, not user accounts. VM100's computer object had been sitting in AD's default `Computers` container since the domain join — that container isn't a true OU and can't have a GPO linked to it directly, so no GPO built so far (all linked to OUs) could ever reach it.

## Reasoning

Linking the software-deployment GPO at the domain root would have technically worked, since every computer object in the domain sits underneath it — but that scopes a mandatory, silent software install to literally every current and future machine in the domain, including the DC itself. A dedicated `Workstations` OU keeps computer-targeted policy scoped only to actual client machines, mirroring the same principle already used for `Departments` (user-targeted policy) — separate containers for separate categories of object, each with a GPO scoped only as broadly as it needs to be.

## Status

Adopted.

## Related

- [Active Directory](../Projects/Active%20Directory.md)
