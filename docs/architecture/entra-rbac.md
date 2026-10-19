# ADR: Entra ID and RBAC

## Status

Accepted (spec v1.1)

## Context

Enterprise self-hosting requires Microsoft Entra sign-in and workspace-scoped authorization.

## Decision

- OIDC authorization code + PKCE; server session after ID token validation.
- **ExternalIdentity** links Entra `oid` to **User**.
- Five roles; fine-grained permissions via static `ROLE_PERMISSIONS`.
- **`executions:infra`** gates mutating Helm/Terraform apply (default: owner, admin).

## Consequences

- Mock OIDC in CI; no production tenant secrets in tests.
- Optional `AUTH_PROVIDER=local` for development only.
- Post-MVP: Entra group → role mapping.
