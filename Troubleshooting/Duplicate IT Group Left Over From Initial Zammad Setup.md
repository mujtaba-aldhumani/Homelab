Duplicate IT Group Left Over From Initial Zammad Setup

## Symptom

Two Zammad Groups existed — `IT` and `IT Support` — rather than the single `IT Support` group intended by [Single IT Support Group over Per-Department Groups for Zammad](../Decisions/Single%20IT%20Support%20Group%20over%20Per-Department%20Groups%20for%20Zammad.md). Surfaced while investigating a separate issue ([Zammad Owner Dropdown Missing Newly-Created Agent](Zammad%20Owner%20Dropdown%20Missing%20Newly-Created%20Agent.md)), where Taylor Morgan's user record showed access to both groups.

## Diagnosis

Reviewed Taylor's Group Permissions table in Manage → Users, which listed both `IT` and `IT Support` with identical Read/Create/Change access — confirming the extra group wasn't a display artifact but a real, separate Group record.

## Root Cause

Zammad ships with a default group (renamed to "IT Support" during initial setup) alongside which a second, separate "IT" group had also been created at some point during setup — leaving two overlapping groups instead of the single intended one.

## Fix

Set the `IT` group's status to Inactive and removed its association from all users, leaving `IT Support` as the sole active group handling tickets.

## Takeaway

When renaming Zammad's default group as part of initial setup, double check no second group was also created alongside it — overlapping groups with the same users and access levels are easy to create by accident and only surface later, often while debugging something else entirely.
