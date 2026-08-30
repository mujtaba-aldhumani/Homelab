GPO Software Package Browser Defaults to Local Drive

## Symptom

When adding a new software package to a GPO (Computer Configuration > Software Installation > New > Package...), the file browser only showed local drives and folders on DC01 itself — no way to find the network share holding the installer.

## Diagnosis

The install source for GPO-deployed software must be a UNC network path (e.g. `\\DC01\SoftwareDeploy\...`), since every targeted computer's SYSTEM account needs to reach the file over the network at boot time — a local `C:\` path means nothing to any machine except DC01. The file the browser needed was known to exist and correctly shared, so the issue was purely how the dialog was being navigated, not a missing file or a permissions problem.

## Root Cause

The package-selection dialog is a standard Windows file picker, which opens to a local-drive view by default — it doesn't automatically browse network shares just because the target needs one.

## Fix

Typed the full UNC path directly into the dialog's "File name" field (`\\DC01\SoftwareDeploy\`) and pressed Enter, which navigated straight into the share. Clicking "Network" in the dialog's left-hand sidebar instead of browsing local folders is an equally valid way to reach the same place.

## Takeaway

Any Windows file-picker dialog can take a typed UNC path directly in the file name field — useful any time a browse dialog defaults to local drives but the target is actually a network location.

## Related

- [Active Directory](../Projects/Active%20Directory.md)
