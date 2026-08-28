Domain User Login Denied on Domain Controller

## Symptom

Attempted to log into DC01 directly as a regular domain user (alex.chen) to test a newly-applied Group Policy. Got a "This sign-in method isn't allowed by your administrator" error after successfully changing the temporary password.

## Diagnosis

The password change itself succeeded, meaning the account and credentials were valid — the failure happened specifically at the local logon step on the domain controller.

## Root Cause

Domain controllers deny local logon to regular domain users by default, via the "Deny log on locally" restriction in the Default Domain Controllers Policy. This is intentional security hardening — only privileged groups (Domain Admins, Server Operators, etc.) are meant to log on directly to a DC, since it's one of the most sensitive machines in the environment. Regular employees are expected to log into their own workstation, not the server hosting the directory itself.

## Fix

No fix needed — this is expected, correct behavior, not a bug. Verification of user-level settings that would normally require an interactive logon (like Group Policy application) was done instead via the Group Policy Modeling Wizard, run from an administrator session, which doesn't require the target user to actually log in anywhere. Domain-joining a separate client VM (VM100) was the real long-term fix, giving regular users somewhere they're actually allowed to log in.

## Takeaway

Never test regular-user behavior by logging into the DC itself — it will always be blocked by design. Use Group Policy Modeling for policy verification, and a domain-joined client machine for anything that needs a real interactive logon.
