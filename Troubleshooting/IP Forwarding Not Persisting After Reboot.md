IP Forwarding Not Persisting After Reboot (Silently Reverted Mid-Session)

## Symptom

Ran `sudo tailscale up --advertise-routes=192.168.86.0/24 --advertise-exit-node` on the `tailscaleproxy` VM, expecting it to succeed (subnet routing had already been working earlier in the same session). Instead got:

```
Warning: IP forwarding is disabled, subnet routing/exit nodes will not work.
See https://tailscale.com/s/ip-forwarding
```

This was unexpected — forwarding had already been enabled and tested successfully earlier for the subnet route.

## Diagnosis

Checked the live kernel state directly instead of assuming:

```
sudo sysctl net.ipv4.ip_forward net.ipv6.conf.all.forwarding
```

Both returned `0` — forwarding was in fact off, contradicting the assumption it was still enabled from earlier.

Checked whether the persistence file existed:

```
cat /etc/sysctl.d/99-tailscale.conf
```

Result: `No such file or directory`. The file had never actually been created.

## Root Cause

Earlier in the session, `sudo sysctl -w net.ipv4.ip_forward=1` (and the ipv6 equivalent) had been run — but this only sets the value in the running kernel, in memory, until the next reboot or reset. The follow-up step to persist it into `/etc/sysctl.d/99-tailscale.conf` was planned via `nano`, but that editing session was abandoned partway through (see Dante config troubleshooting) without the persistence step ever being redone afterward. The temporary in-memory value was presumably reset by something in between (a reboot, or a service/process resetting sysctl state) without that being noticed at the time, since nothing was actively depending on it yet.

## Fix

Wrote the persistence file directly from the command line instead of relying on an interactive editor, then reloaded it immediately:

```
sudo tee /etc/sysctl.d/99-tailscale.conf > /dev/null << 'EOF'
net.ipv4.ip_forward=1
net.ipv6.conf.all.forwarding=1
EOF
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```

Re-ran the Tailscale advertise command afterward — succeeded cleanly (only the unrelated, informational UDP GRO forwarding warning remained).

## Takeaway

A `sysctl -w` change is temporary by design — always verify the actual persistence file was written, not just that the command ran without error. Verifying live state (`sysctl <key>`) directly is more reliable than trusting that an earlier step in a session actually completed as planned, especially if that step was interrupted.
