Privileged over Unprivileged LXC for Zammad

## Decision

Deployed the Zammad LXC container (VMID 104) as Privileged rather than the script's Unprivileged default.

## Context

The Proxmox Community Scripts Zammad installer defaults to an Unprivileged container unless Advanced Settings is used to override it. Unprivileged containers have a documented history of Elasticsearch (one of Zammad's required services) failing to start, due to permission errors on kernel-level operations that unprivileged containers restrict.

## Reasoning

Since Elasticsearch is the single most common failure point reported for this install, and the container's only real purpose is running this one application stack, the isolation benefit of staying unprivileged was judged not worth the added risk of a flaky install. Privileged mode gives Elasticsearch an easier path to starting reliably, at the cost of a weaker container security boundary — an acceptable trade for a single-purpose lab service.

## Alternatives Considered

Unprivileged with live troubleshooting if Elasticsearch failed — rejected as the primary plan (though it ended up happening anyway on the first build attempt, when the setup wizard couldn't be navigated back to change Container Type before confirming — see [Interrupted Zammad Install Leaves Container Running With No Services](../Troubleshooting/Interrupted%20Zammad%20Install%20Leaves%20Container%20Running%20With%20No%20Services.md)). The container was destroyed and rebuilt privileged from scratch to actually land on this decision.

## Status

Adopted. Elasticsearch confirmed stable (`active (running)`, no crash loop) after the privileged rebuild.

## Related

- [Zammad](../Projects/Zammad.md)
