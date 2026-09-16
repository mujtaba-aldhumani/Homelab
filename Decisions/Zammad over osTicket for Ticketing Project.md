Zammad over osTicket for Ticketing Project

## Decision

Chose Zammad over osTicket as the platform for the helpdesk/ticketing homelab project.

## Context

Both are open-source, self-hostable ticketing systems commonly cited as options for a homelab helpdesk project. osTicket is a classic PHP/MySQL LAMP-stack app; Zammad is a Ruby on Rails app requiring PostgreSQL, Redis, and Elasticsearch behind an nginx reverse proxy.

## Reasoning

The deciding factor was which platform teaches more transferable sysadmin skill relative to career interests (sysadmin/cloud/networking/security, not software development). osTicket's LAMP stack is simpler and faster to stand up, but it's a single-service troubleshooting exercise. Zammad's multi-service architecture (several interdependent services that all have to start, stay healthy, and talk to each other correctly) is a closer analog to real-world infrastructure problems, and its UI/workflow is closer to what a modern support org (Zendesk/Freshdesk-style) actually looks like. The added complexity and higher resource cost were accepted as a worthwhile trade for the more realistic multi-service orchestration experience.

## Alternatives Considered

osTicket — faster, lower-risk, but teaches a narrower and more dated skill set (single LAMP app rather than multi-service orchestration).

## Status

Adopted.

## Related

- [Zammad](../Projects/Zammad.md)
