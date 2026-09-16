Zammad systemd Units Show sleep infinity as Main Process

## Symptom

`systemctl status zammad`, `zammad-web`, `zammad-worker`, and `zammad-websocket` all reported `active (running)`, but each unit's Main PID resolved to `/bin/sleep infinity` rather than an actual Puma, Rails, or worker process.

## Diagnosis

Checked what was actually listening and running, independent of what systemd reported as each unit's "main" process: `ss -tlnp | grep -E ':80|:3000|:6042'` and `ps aux | grep -E 'puma|rails|ruby'`.

## Root Cause

Zammad's official systemd packaging uses lightweight supervisor/umbrella units for `zammad`, `zammad-web`, `zammad-worker`, and `zammad-websocket`; the actual application processes run as separate children not tracked as each unit's own Main PID. `sleep infinity` is a placeholder keeping the unit itself alive, not a sign the real workload is stuck sleeping.

## Fix

No fix needed. Confirmed genuinely healthy: a real Puma process (`puma 7.2.1`) bound to `127.0.0.1:3000`, a ruby process bound to `127.0.0.1:6042` (websocket), and nginx listening on port 80 and proxying through.

## Takeaway

When a service's packaging is unfamiliar, verify health via listening ports and the actual process list, not just `systemctl status`'s reported Main PID — some packaging patterns use supervisor/placeholder units that look alarming but are functioning as designed.
