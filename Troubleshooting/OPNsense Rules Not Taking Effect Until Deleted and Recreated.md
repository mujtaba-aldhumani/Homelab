OPNsense firewall rules not taking effect despite correct config and Apply

## Symptom

After the DHCP, outbound HTTP/HTTPS, and DNS ("DOMAIN") rules had all been added correctly to the Clients interface and **Apply** had been clicked, traffic still behaved exactly as if none of the rules existed — `apt update` on `client-test` kept failing with the same DNS-resolution errors as before the rules were added, and the Firewall Live View log showed a "Default deny / state violation rule" block on a DNS packet. That log entry later turned out to be stale (timestamped well before the most recent retry — see below), which briefly caused extra confusion before being caught.

## Diagnosis attempted (partially unproductive)

- Confirmed rule content and order visually in Firewall → Rules → Clients — everything looked correct
- Confirmed Outbound NAT was in Automatic mode — ruled out
- Checked Firewall → Log Files → Live View, filtered to WAN only by default, initially showed unrelated background broadcast traffic (mDNS/SSDP) with no relevance; had to manually adjust the filter to Clients to see anything relevant
- The one relevant block entry found in the log was later determined to be from an earlier attempt (timestamp ~20 minutes older than the actual retry), not evidence of the current state — a reminder to always check the timestamp on a log entry before treating it as current evidence
- Pass rules are not logged by default in OPNsense (only blocks are), so "no new entries" during a live retry did not mean traffic was still being blocked — it was ambiguous either way, which is why log-based diagnosis stalled here

## Fix

Deleted the affected rules on the Clients interface entirely and recreated them from scratch (same settings as before), then applied. This immediately resolved the issue — `apt update`, `curl`, and the final inter-VLAN HTTP test all worked correctly afterward.

## Likely cause

Not confirmed with certainty, but the most plausible explanation is a stale firewall state-table issue: OPNsense's underlying packet filter (`pf`) can, in some cases, continue evaluating connections against cached state from before a series of rapid rule edits, even after clicking Apply. A full delete-and-recreate forces a clean rebuild of the rule (and its associated state) rather than an in-place edit.

## Lesson

If a rule's configuration and Apply status both look correct but traffic still behaves as if the rule doesn't exist, deleting and recreating the rule is a legitimate, fast troubleshooting step — not just a "fix by brute force" with no explanation. Also: always check log entry timestamps before treating them as evidence of the current state, and remember that OPNsense does not log passed traffic by default, only blocked traffic — so an empty live log during a retry is not proof that nothing changed.
