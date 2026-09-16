Own Admin Account over Separate Agent Persona for Zammad

## Decision

Initially decided to work Zammad tickets from the existing personal admin account (with the Agent role added) rather than creating a separate fictional agent persona.

## Context

Three fictional customer users already existed (Sam Patel, Alex Chen, Jordan Lee), mirroring the Active Directory departments. The question was who should represent the person actually working tickets in Zammad — a new fictional identity, or the same account already used to administer the system.

## Reasoning

The parallel initially considered was the AD project's separate admin-vs-daily-use-account principle, but that didn't quite map — the real distinction here is "who configured Zammad" vs. "who works the helpdesk queue," and in a very small company or one-person IT shop it's entirely normal and realistic for the same person to do both. Given the AD project was already modeling a small company, using one account for both roles (Admin + Agent, roles are additive in Zammad) was judged simpler and equally realistic.

## Status

Superseded. A separate "Taylor Morgan" Zammad Agent account was created after all, once the project was extended to include a delegated Active Directory helpdesk-tech identity — see [Zammad](../Projects/Zammad.md). Keeping the same "Taylor Morgan" identity consistent across both Zammad (who owns and works the ticket) and Active Directory (who has delegated permission to actually fix it) made the end-to-end simulation more coherent than splitting the ticket-handling identity from the AD identity.

## Related

- [Zammad](../Projects/Zammad.md)
