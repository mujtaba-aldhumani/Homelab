DNS-Level Blocking Ineffective Against Streaming-Site Popup and Redirect Ads

## Symptom

After pointing Google Wifi's DNS at Pi-hole, tested ad blocking against a free movie-streaming website. Popup and redirect ads still occurred normally, seemingly unaffected by Pi-hole.

## Diagnosis

Checked Pi-hole's dashboard first — total query count, block percentage, and domain list all looked healthy and active, so the setup wasn't obviously broken. The more likely explanation was the type of ad delivery this particular kind of site uses: popups and redirects on these sites are commonly triggered by JavaScript embedded directly in the page itself (often served from the same domain as the page/player), or by hijacking a click anywhere on the page, rather than by loading a resource from a separate, blockable third-party ad-network domain. Some of these sites also rotate ad-network domains specifically to dodge blocklists like the one in use (StevenBlack/hosts).

## Root Cause

Pi-hole blocks DNS lookups to known ad/tracker domains; it has no way to intercept ads or redirects that don't require a separate, blocklisted domain lookup. This is an inherent limitation of DNS-level ad blocking in general, not a misconfiguration of this setup.

## Fix

Not applicable — not a bug. Confirmed the setup was actually working by testing against Pi-hole's own dedicated test domain (`ads-tracking.pi-hole.net`) and by checking the query log for real ad-network domains getting blocked during normal browsing, instead of relying on an aggressive streaming site as the test case.

## Takeaway

Pick a reliable, purpose-built test (Pi-hole's own test domain, or a site with standard third-party ad networks) when verifying ad blocking, rather than an ad-injecting streaming site — a "failure" there doesn't necessarily mean anything is wrong. If blocking this style of ad ever becomes a priority, the realistic options are browser-level blocking (e.g. uBlock Origin) or streaming-specific blocklists, not more DNS-level tuning.
