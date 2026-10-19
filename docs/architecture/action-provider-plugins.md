# ADR: Action provider plugins

## Status

Accepted (spec v1.1)

## Context

Integrations (REST, Kafka, MQTT) and infrastructure (Helm, Terraform) must extend without rewriting the executor.

## Decision

- **ActionProviderRegistry** maps `action:<type>` → module under `providers/<type>/`.
- Shared Zod schemas in `src/lib/schemas/providers/<type>.ts`.
- Separate **editor provider UI** plugins (optional Skeleton forms).
- Phase 4: rest, kafka, mqtt. Phase 5: helm, terraform (pinned CLIs, not shell).

## Consequences

- `DEVDOCS_PROVIDERS` env may gate types in self-hosted deployments.
- Provider docs in `docs/providers/`.
