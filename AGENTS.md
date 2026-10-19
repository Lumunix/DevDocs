# DevDocs — agent guide

DevDocs is a Markdown-first document platform where prose meets execution. This
repo is primarily specification-driven until the SvelteKit app is scaffolded.

## Canonical spec

Read **[docs/devdocs-product-spec.md](docs/devdocs-product-spec.md)** (v1.1) before
implementing. Follow phased delivery in §19; do not skip security or RBAC.

## Stack (target)

- **SvelteKit** + TypeScript
- **Skeleton UI v5** — all product UI ([skeleton.dev](https://www.skeleton.dev/))
- **Milkdown Crepe** — document editor (client-only)
- **@egjs/svelte-infinitegrid** — browse grids with infinite scroll
- **PostgreSQL** — durable data
- **Worker** + queue — action execution (not in HTTP requests)
- **Entra ID** — OIDC login (local `AUTH_PROVIDER=local` dev only)

## Architecture ADRs

See [docs/architecture/](docs/architecture/).

## Commands (once scaffolded)

Document actual scripts in `package.json` when present. Expected:

- `npm run dev` — web app
- `npm run test` / `npm run test:integration` / `npm run test:e2e`
- `npm run lint` / `npm run check` / `npm run build`
- `docker compose up` — local stack
- migrations via project convention (e.g. `npm run db:migrate`)

## Non-negotiables

1. **Markdown first** — stored `body_markdown`; remark/unified parser is canonical for validate/run.
2. **No client-side execution** — actions run in the worker only.
3. **RBAC** — use `requirePermission`; never trust client role hints.
4. **Secrets in resources** — never in Markdown, logs, or API responses.
5. **SSRF** — REST provider egress controls are release blockers.
6. **Incremental phases** — keep the app runnable after each phase.

## Layout (target)

Server-only code under `src/lib/server/` and `worker/`. Providers under
`src/lib/server/execution/providers/`. Editor under `src/lib/components/editor/`.
See spec §16.

## Cursor rules

Project rules live in `.cursor/rules/devdocs-*.mdc`.
