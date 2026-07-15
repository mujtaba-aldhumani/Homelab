raw.githubusercontent.com Intermittent DNS Resolution Failures (Proxmox Community Scripts)

## Symptom

Multiple Pi-hole LXC build attempts failed partway through with:

```
curl: (6) Could not resolve host: raw.githubusercontent.com
```

This happened both inside the Proxmox Community Script's own automation and later inside Pi-hole's own official installer, always shortly after a freshly-built container's network came up.

## Diagnosis

Ruled out a host-level DNS problem first: from the Proxmox host itself, `cat /etc/resolv.conf` showed a valid nameserver (`8.8.8.8`), and `ping raw.githubusercontent.com` resolved and responded normally. So the Proxmox host's own DNS/internet access was never the problem.

Also confirmed the container's assigned static IP/gateway were correct at the time of failure (`pct config <id>` matched the intended `192.168.86.202` / `192.168.86.1`), ruling out a subnet misconfiguration as the cause on the runs where this specific error occurred.

Researched the exact error message afterward and found it's a widely-documented, known-flaky issue reported across multiple Proxmox community script installs (not specific to Pi-hole, and not specific to this homelab) — commonly attributed to IPv6 route flakiness immediately after a fresh container's network interface comes up (even when IPv6 was set to "none" in the wizard, containers were still observed picking up a link-local/auto IPv6 address), or to GitHub's raw-content CDN itself being intermittently unstable.

## Root Cause

Not conclusively isolated to one specific mechanism — most likely a transient IPv6 connectivity race condition immediately after container network bring-up, compounded by GitHub's raw-content CDN having its own occasional instability. Confirmed as transient rather than persistent: identical container builds on other attempts, with no configuration changes, completed with all DNS checks passing cleanly.

## Fix

No permanent fix was needed — subsequent build attempts succeeded without intervention. If this recurs and blocks progress, the documented options are:

- Disable IPv6 at the OS level inside the container: `echo 'net.ipv6.conf.all.disable_ipv6 = 1' >> /etc/sysctl.conf && sysctl -p`
- Force IPv4-only DNS/connections for the affected `curl`/`wget` calls (`-4` flag)

## Takeaway

Not every failure mid-install is a configuration mistake — some upstream dependencies (GitHub's CDN, a container's just-initialized network stack) are simply flaky in ways outside the setup's control. Worth confirming host-level connectivity is fine first (rules out the most likely local cause), then treating a one-off failure as possibly transient and retrying before assuming something is fundamentally misconfigured.
