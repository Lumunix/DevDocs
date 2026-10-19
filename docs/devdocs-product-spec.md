# DevDocs --- Product Brief and Implementation Specification

**Document version:** 1.1\
**Prepared:** October 13, 2026\
**Audience:** Coding agents (Cursor, Claude Code, Codex, or similar) and
engineers\
**Project name:** DevDocs\
**Status:** Initial product direction and MVP specification

> **Purpose:** Use this document as the implementation brief for
> DevDocs. The agent should first inspect the target repository, then
> implement the product incrementally. This is a product specification,
> not a claim that any code has already been implemented.

------------------------------------------------------------------------

## 1. Executive summary

**DevDocs is a Markdown-first document platform where prose meets
execution.**

Users author readable Markdown documents that can include structured
action blocks. The document serves as both the explanation of a
procedure and the definition of an executable operation. Built-in action
providers include REST, Kafka, and MQTT (integration); Helm and Terraform
(infrastructure) follow in a later phase.

DevDocs should be a self-hostable web application built with
**SvelteKit, TypeScript, and Skeleton UI**. It should provide document
organization, editing and preview, validation, controlled execution,
execution history, logs, and reusable integration resources.

The product should not merely be a CLI that executes Markdown files. The
intended experience is a full browser-based application for creating,
managing, validating, and running documents.

### Product positioning

> DevDocs --- A Markdown-based document platform where prose meets
> execution. Create living documents that orchestrate workflows, execute
> REST requests, publish Kafka/MQTT messages, run controlled Helm and
> Terraform steps, and automate operations through embedded instructions.

### Core idea

Traditional documentation describes what a person should do. Traditional
workflow engines define what a machine should do. DevDocs should bring
these together: a document explains the operation in human-readable
language and defines structured, executable actions alongside that
explanation.

------------------------------------------------------------------------

## 2. Conversation and design decisions

The design direction emerged from exploring names that combine
documents, scripts, actions, Markdown, and workflows.

The selected name is **DevDocs**.

The user intends to build a **web app**, preferably using: - SvelteKit -
TypeScript - [Skeleton UI](https://www.skeleton.dev/) - Markdown as the
primary document format

The platform should support automation instructions such as: - REST/HTTP
requests - Publishing Kafka messages - Passing outputs from one action
to another - Eventually, additional integration providers and workflow
capabilities

Existing projects worth studying include: -
[Runme](https://github.com/runmedev/runme) --- executable Markdown and
DevOps-oriented documents - [upmd](https://github.com/rezigned/upmd) ---
Markdown tasks and dependencies - [Rundown](https://rundown.cool/) ---
executable operational procedures -
[Windmill](https://github.com/windmill-labs/windmill) --- web-based
scripts and workflow automation - [Kestra](https://github.com/kestra-io)
--- declarative workflow orchestration -
[n8n](https://github.com/n8n-io/n8n) --- visual automation - [Jupyter
Notebook](https://github.com/jupyter/notebook) --- interactive documents
combining prose and execution -
[khalidx/runbook](https://github.com/khalidx/runbook) --- Markdown
commands with a web interface

These are reference projects, not required dependencies. Inspect their
current licenses, activity, architecture, and capabilities before
deciding to reuse any code or infrastructure. Do not assume any project
is a drop-in replacement.

### Primary design references

| Reference | What to borrow | What not to copy |
|-----------|----------------|------------------|
| [BookStack](https://github.com/bookstackapp/bookstack) | Tagging, search prominence, filter chips/facets, low-friction browse | PHP/Laravel stack; fixed Book/Chapter hierarchy |
| [@egjs/svelte-infinitegrid](https://github.com/naver/egjs-infinitegrid) | Masonry/card grids, infinite scroll via `requestAppend` | Using grid for editor layout |
| [Milkdown Crepe](https://milkdown.dev/docs/guide/using-crepe) | WYSIWYG Markdown, `crepe.editor` for plugins; theme aligned with Skeleton | ProseMirror doc as source of truth instead of Markdown |
| [Skeleton v5](https://www.skeleton.dev/) | Svelte components, themes, dark mode, layouts, forms | Legacy Skeleton v2--v4 APIs; copying demos without adaptation |

**Skeleton** is the **required** UI stack for DevDocs application surfaces.
Other references are inspirational unless explicitly chosen in §5.

**BookStack-inspired behaviors (DevDocs data model):**

-   Workspace-scoped **tags** on documents (many-to-many).
-   **Search** across title, body, tags, and folder path; filters for tag,
    status, folder, and updated range.
-   Browse views (recent, by tag, by folder) without adopting
    BookStack's Shelf/Book/Chapter hierarchy---keep **Folder** + **Document**.

### Product differentiation

DevDocs should distinguish itself through: 1. **Markdown-first
authoring:** the document is the primary interface and source of truth.
2. **First-class actions:** REST, Kafka, MQTT, Helm, Terraform, and
future providers are explicit structured action types via a modular
registry. 3. **Human-readable procedures:**
explanatory prose and executable operations live together. 4.
**Controlled execution:** permissions, validation, explicit run
controls, logs, and audit history. 5. **Self-hostability:** practical
local development and container-based deployment. 6. **Extensibility:**
new action providers can be added without rewriting the editor or
execution engine.

------------------------------------------------------------------------

## 3. Product principles

1.  **Markdown first.** The underlying document remains readable
    Markdown, including action blocks as ordinary fenced code blocks.
2.  **Web application first.** Users should not need a terminal or
    editor plugin to author and execute documents.
3.  **Explicit execution.** Viewing or saving a document never runs it
    automatically.
4.  **Safe by default.** No arbitrary shell, JavaScript, or Python
    execution in the initial MVP.
5.  **Observable execution.** Each run and step has a durable status,
    timing, logs, and structured outputs.
6.  **Secrets are resources, not document text.** Credentials must never
    be stored in Markdown or exposed to the browser.
7.  **Portable source.** Users can export the original Markdown without
    losing the action definitions.
8.  **Incremental delivery.** Build a runnable product in phases instead
    of generating a huge, untested implementation.
9.  **Reuse existing repository conventions.** Inspect the repository
    before replacing tooling or architecture.
10. **Security is a release requirement.** Execution security, access
    control, secret handling, and SSRF prevention are not optional
    polish.
11. **Adaptive UI.** Skeleton-based surfaces must remain usable on
    mobile, tablet, and desktop (see §5 and §6).

------------------------------------------------------------------------

## 4. Scope

### 4.1 MVP features

-   SvelteKit application using TypeScript and **Skeleton UI v5**.
-   Sign-in with **Microsoft Entra ID** (OAuth 2.0 / OIDC, PKCE);
    workspace **RBAC** with roles and fine-grained permissions; optional
    `AUTH_PROVIDER=local` for development only.
-   Document **tags** (create, assign, filter); search and filterable browse.
-   Document creation, editing, saving, viewing, search, and export.
-   Folder/collection organization.
-   **Milkdown Crepe** editor with distinct **action blocks**; raw Markdown
    export and optional read-only source view.
-   **@egjs/svelte-infinitegrid** for document and execution browse views
    with cursor-based pagination.
-   Structured action blocks embedded in Markdown.
-   Action parsing and validation with helpful errors.
-   Typed document inputs.
-   **Modular ActionProviderRegistry**; Phase 4 providers: REST, Kafka, MQTT.
-   Sequential action execution.
-   Passing typed output from an earlier action to a later action.
-   Named integration resources with server-side credentials.
-   Durable execution history and per-step logs/results.
-   Document revision snapshots.
-   Worker process separate from the web request lifecycle.
-   Database migrations, tests, Docker Compose, and deployment
    documentation.

### 4.2 Out of scope for MVP

-   Arbitrary user-provided shell or programming-language execution (Helm
    and Terraform providers use **pinned CLIs with schema-bound arguments**
    only---not a shell escape hatch).
-   Product/runtime **MCP** server exposing Validate/Run to external agents
    (future optional extension).
-   Entra **group → role** auto-mapping, SCIM, and multi-IdP (Google,
    GitHub) unless required for local dev.
-   Anonymous public execution links.
-   SaaS billing and multi-tenant commercial administration.
-   Real-time collaborative editing.
-   Visual drag-and-drop workflow designer.
-   Complex DAGs, parallel branches, loops, human approval gates, and
    durable long-running workflows.
-   Automatic execution when a document is opened or saved.
-   AI-generated actions.
-   Kafka consumer triggers or event-driven document starts.
-   Replacing a general-purpose secrets manager.

Keep the architecture extensible enough that these can be considered
later without implementing them prematurely.

------------------------------------------------------------------------

## 5. Technology direction

### Frontend

**Skeleton UI v5 (mandatory for app surfaces)**

-   **SvelteKit + TypeScript** with [Skeleton v5](https://www.skeleton.dev/)
    per [installation](https://www.skeleton.dev/docs/svelte/get-started/installation)
    and [fundamentals](https://www.skeleton.dev/docs/svelte/get-started/fundamentals).
-   Do not introduce parallel UI kits for product screens; wrap Skeleton
    primitives or Skeleton Tailwind utilities.
-   Prefer Skeleton framework components where they fit: App Bar,
    Navigation, Menu, Dialog, Toast, Tabs, Tags Input, Combobox, Listbox,
    Table, Card, Chips, Forms, Progress, Tree View (folders).
-   Theming via Skeleton themes and [dark mode](https://www.skeleton.dev/docs/guides/dark-mode).
-   Use **current v5 APIs only**; do not copy Skeleton v2--v4 examples.

**Editor and lists**

| Layer | Choice | Role |
|-------|--------|------|
| App shell | Skeleton v5 + Tailwind | Login, `/app` routes, settings, resources |
| Authoring | `@milkdown/crepe` + Svelte ([Svelte recipe](https://milkdown.dev/docs/recipes/svelte)) | Primary edit; client-only (`ssr: false` or dynamic import) |
| Action blocks | Core Milkdown plugin + per-provider editor plugins | Serialize to `action:<type>` fences |
| Browse | `@egjs/svelte-infinitegrid` in Skeleton cards | Documents, executions, search results |
| Parse/validate | unified/remark on server | Canonical grammar for validate and run |

-   [Using Crepe](https://milkdown.dev/docs/guide/using-crepe): themes,
    `getMarkdown()`, listeners, `crepe.editor` for custom plugins.
-   Crepe/Milkdown CSS should harmonize with Skeleton tokens (editor chrome bridge).
-   Safe Markdown rendering for read-only views; raw HTML disabled by default.
-   Runtime schemas using Zod (shared with provider config where feasible).

**Responsive layout (release requirement)**

-   Mobile-first Tailwind breakpoints; align with Skeleton
    [Layouts](https://www.skeleton.dev/docs/guides/layouts).
-   **Mobile** (~320--639px): single column; collapsible nav; full-width
    editor; filters in Dialog or disclosure.
-   **Tablet** (~640--1023px): ~2-column grids; wrapped toolbars; execution
    in tab or bottom sheet.
-   **Desktop** (~1024px+): sidebar + main + execution drawer; 3+ grid columns.
-   Infinite grid reflows by breakpoint; ~44px minimum tap targets on primary actions.

References: - SvelteKit: https://svelte.dev/docs/kit/introduction - SvelteKit
routing: https://svelte.dev/docs/kit/routing

### Backend and data

Recommended starting architecture: - SvelteKit for the web UI and
application API - PostgreSQL as the durable source of truth - A separate
Node.js/TypeScript worker for action execution - Redis/Valkey plus
BullMQ for queueing if appropriate - KafkaJS or another maintained Node
Kafka client for publishing - Database migrations checked into source
control - Docker Compose for local development and self-hosted
deployment

Do not run long-running action execution inside a normal HTTP request.
PostgreSQL must remain the durable record of executions; queue state
alone is insufficient.

If the existing repository already contains a good architecture, inspect
it and preserve what makes sense rather than replacing it without
justification.

------------------------------------------------------------------------

## 6. Target user experience

The authenticated application should feel like a focused developer
documentation workspace.

### Main layout

-   Left sidebar (desktop) or App Bar menu (mobile) with workspace
    navigation, search, folders, documents, executions, resources, members,
    and settings.
-   **Documents index** (`/app/documents`): masonry/card grid with infinite
    scroll; search plus tag, status, and folder filters.
-   **Executions index** (`/app/executions`): similar grid with status,
    document, and date filters.
-   Main pane: **Milkdown Crepe** for editing; Skeleton toolbar with title,
    save status, revision, **Validate**, and **Run**.
-   Optional **Tabs** for raw Markdown (read-only) or export; Crepe is default
    (no permanent side-by-side source split unless user opens source view).
-   Execution panel: **drawer/split on desktop**; **Tabs or Dialog on mobile**.
-   Light/dark theme using Skeleton theming.
-   Responsive, accessible controls and keyboard navigation.

Avoid spending the initial effort on a marketing site. Prioritize the
authenticated application.

### Main routes

Suggested SvelteKit routes: - `/login` - `/app` --- overview and recent
activity - `/app/documents` - `/app/documents/new` -
`/app/documents/[documentId]` - `/app/documents/[documentId]/edit` -
`/app/executions` - `/app/executions/[executionId]` - `/app/resources` -
`/app/resources/new` - `/app/settings`

Adapt route conventions to the actual repository.

------------------------------------------------------------------------

## 7. Markdown format

DevDocs documents are ordinary Markdown with optional frontmatter and
explicitly named action code fences.

### Example document

```` markdown
---
devdocs_version: 1
title: Provision a customer
description: Create a customer and notify downstream services.
inputs:
  customer_name:
    type: string
    required: true
---

# Provision a customer

This procedure creates a customer and notifies downstream services.

## Step 1: Create the customer

```action:rest id=create_customer
resource: customer-api
method: POST
path: /customers
headers:
  Content-Type: application/json
body:
  name: "{{ inputs.customer_name }}"
  source: devdocs
timeout_ms: 10000
```

## Step 2: Publish the event

```action:kafka id=publish_customer_created
resource: customer-events
topic: customer.created
key: "{{ steps.create_customer.output.id }}"
payload:
  customerId: "{{ steps.create_customer.output.id }}"
  name: "{{ inputs.customer_name }}"
```
````

This is illustrative syntax. Implement and document a formal grammar and
schemas; do not parse action blocks using fragile regular expressions
alone.

### Parsing rules

-   Parse Markdown into an AST using a maintained parser.
-   Recognize `action:<provider>` fenced blocks using a documented
    grammar.
-   All action IDs must be unique within a document.
-   Ordinary fenced code blocks remain display-only and never execute.
-   Parse and validate each action's YAML/configuration and provider
    schema.
-   Return actionable validation errors with line and column locations
    where feasible.
-   Unknown providers must produce explicit validation errors, not be
    silently ignored.
-   Frontmatter is metadata, never executable code.
-   Enforce document size, action count, and configuration size limits.
-   Version the document format and define migration behavior before
    making breaking syntax changes.

### Variables and references

Use a deliberately restricted reference syntax: -
`{{ inputs.customer_name }}` - `{{ steps.create_customer.output.id }}`

MVP rules: - No arbitrary expressions or function execution. - Only
known inputs and outputs of completed earlier steps can be referenced. -
Reject unknown or forward references. - Preserve JSON types rather than
performing blind string substitution inside JSON. - Define handling for
strings, numbers, booleans, arrays, objects, nulls, and missing
values. - Escape and encode values appropriately when used in URLs,
headers, and text fields. - Never interpolate secret values into logs.

### Document inputs

Support at least: - string - number - boolean - enum - optional values
and defaults

Validate user-provided inputs before enqueueing an execution. Inputs
must not be able to override server-managed credentials or resource
secrets.

### Editor serialization

-   Stored `body_markdown` comes from Crepe `getMarkdown()` on save.
-   Action blocks remain ordinary fenced blocks (`action:rest`, etc.) in
    stored Markdown.
-   The Milkdown action-block plugin **parse ↔ serialize** those fences;
    server remark parser rules in this section remain **canonical**.
-   Map validation errors to Markdown line/column where feasible; document
    gaps when only ProseMirror positions exist.

------------------------------------------------------------------------

## 8. Action providers

The action system must be extensible. Each provider should define: -
Stable action type identifier. - Typed schema and validation. - Resource
validation. - A safe action summary or preview. - Server-side
execution. - Typed output. - Timeout and cancellation support where
feasible. - Structured, redacted logs.

Conceptual TypeScript interface:

``` ts
interface ActionProvider<TConfig, TOutput> {
  type: string;
  validate(
    config: unknown,
    context: ValidationContext
  ): ValidationResult<TConfig>;
  execute(
    config: TConfig,
    context: ActionExecutionContext
  ): Promise<ActionResult<TOutput>>;
}
```

The execution context should provide execution and step IDs, validated
input values, outputs from completed steps, safe access to approved
resource credentials, a redacting logger, cancellation/deadline
information, and workspace/actor identity for authorization and
auditing.

Do not give providers unrestricted database or filesystem access.

### Plugin architecture

Two layers must not be conflated:

| Layer | Purpose |
|-------|---------|
| **Action providers** (server/worker) | Validate and execute `action:<type>` blocks |
| **Editor provider UI** (Milkdown/Crepe) | Optional per-type authoring (forms, chips, fence serializer) |

**ActionProviderRegistry** maps `action:<type>` → provider module. One folder
per type under `src/lib/server/execution/providers/<type>/` with `schema.ts`,
`validate.ts`, `execute.ts`, `summary.ts`, `index.ts`. Register types in
`providers/registry.ts`; optional `DEVDOCS_PROVIDERS` env allowlist.

Shared **Zod schemas** live in `src/lib/schemas/providers/<type>.ts` for
editor and server alignment.

**ActionProviderDefinition** (client-safe): `type`, `displayName`,
`configSchema`, `defaultTemplate`, optional Skeleton form panel. Infra providers
(Helm, Terraform) must surface plan/apply or dry-run warnings in the UI.

**Validate path:** parser extracts all action blocks; registry validates each
type; unknown types fail with explicit errors.

**Run path:** worker executes `provider.execute` sequentially in document order.

### 8.1 REST provider

Action type: `action:rest`

Fields: - `resource` --- required named HTTP resource - `method` ---
GET, POST, PUT, PATCH, DELETE - `path` --- relative path beneath the
configured base URL by default - optional `query` - optional `headers` -
optional `body` - optional bounded `timeout_ms` - optional expected
status codes or range

Behavior: - Use server-side resource configuration for the base URL and
authentication. - Reject absolute URLs and host changes by default. -
Enforce connection/request timeouts, response size limits, and redirect
limits. - Revalidate every redirect destination. - Do not automatically
retry non-idempotent requests unless explicitly configured and safe. -
Return structured status and response data, with truncation limits. -
Redact authorization headers, cookies, and configured secrets. -
Validate TLS certificates by default.

### 8.2 Kafka provider

Action type: `action:kafka`

Fields: - `resource` --- required named Kafka connection - `topic` ---
required topic - optional `key` - `payload` --- JSON-compatible value -
optional explicitly allowlisted headers

Behavior: - Broker configuration and credentials live in a server-side
resource. - Do not accept arbitrary broker addresses or credentials from
document inputs. - Serialize payloads as JSON by default. - Return
acknowledgement information such as topic, partition, and offset when
available. - Apply bounded connection and delivery timeouts. - Never log
SASL credentials, certificates, or private keys. - Do not claim
exactly-once delivery unless the implementation truly provides it. -
Kafka publishing is in scope; Kafka consumers/triggers are not in the
MVP.

### 8.3 MQTT provider

Action type: `action:mqtt`

Fields: - `resource` --- required named MQTT connection - `topic` ---
required - `payload` --- JSON-compatible - optional `qos` (0 or 1,
default 0) - optional `retain` (boolean, default false) - optional
bounded `timeout_ms`

Behavior: - Broker configuration and credentials in server-side resource
only. - No subscribe/consumers in MVP. - TLS verify by default; redact
credentials; bounded payload size.

### 8.4 Helm provider (Phase 5)

Action type: `action:helm`

Fields: - `resource` --- Kubernetes connection - `release`, `namespace`,
`chart` (OCI or allowlisted repo/chart/version) - `values` (YAML,
template-interpolated) - optional `operation`: `install` | `upgrade` |
`uninstall` (default `upgrade`) - optional `dry_run` - bounded
`timeout_ms`

Behavior: - Kubeconfig/token from resource only; chart sources must match
**workspace allowlist**. - Invokes **pinned `helm` CLI** with fixed argument
templates---not arbitrary shell. - **`executions:infra`** required for mutating
install/upgrade/uninstall; dry-run may use **`executions:run`** only.

### 8.5 Terraform provider (Phase 5)

Action type: `action:terraform`

Fields: - `resource` --- backend/credentials - `operation`: `plan` |
`apply` (default **`plan`**) - `module` (allowlisted registry module or
root path relative to **execution snapshot** only) - `variables` (map) -
optional TF `workspace` - bounded `timeout_ms`

Behavior: - Remote state credentials in resource only. - **`apply`** requires
**`executions:infra`** and/or workspace flag `allow_terraform_apply`. -
Invokes **pinned `terraform` CLI** in isolated temp dir---not arbitrary shell.
- Out of scope initially: destroy, `-target`, local-exec provisioners.

------------------------------------------------------------------------

## 9. Resources and secret handling

A resource is a named integration configuration that action blocks can
reference.

Initial resource types: - HTTP/REST - Kafka - MQTT - Kubernetes (Helm) -
Terraform (backend/cloud credentials as applicable)

Suggested fields: - ID and workspace ID - Name - Type - Non-secret
configuration - Encrypted secret material or reference to an external
secret store - Created/updated timestamps and actor IDs

Requirements: - Encrypt secrets at rest using a key supplied by
deployment configuration. - Never return existing secret values to the
browser after creation. - Do not put secrets in Markdown, revisions,
execution snapshots, logs, or error messages. - Restrict resource
management to authorized roles. - Support redaction of known secrets
from provider logs and outputs. - Document key rotation and backup
implications.

------------------------------------------------------------------------

## 10. Data model

Use UUIDs or another consistent opaque ID strategy, timestamps, and
workspace boundaries.

### Workspace

-   `id`
-   `name`
-   `created_at`

### User

-   `id`
-   `email`
-   `display_name`
-   `password_hash` nullable (OAuth-only users)
-   `created_at`
-   `disabled_at` nullable

Never store plaintext passwords.

### ExternalIdentity

-   `id`
-   `user_id`
-   `provider` (e.g. `entra`)
-   `issuer`
-   `subject` (oid)
-   `tenant_id`
-   `email`
-   `created_at`
-   Unique `(provider, issuer, subject)`

### WorkspaceMember

-   `workspace_id`
-   `user_id`
-   `role`: `owner`, `admin`, `editor`, `runner`, `viewer`
-   optional `assigned_by`, `assigned_at`
-   Unique constraint on workspace/user

### Tag

-   `id`
-   `workspace_id`
-   `name`
-   `slug`
-   Unique `(workspace_id, slug)`
-   timestamps

### DocumentTag

-   `document_id`
-   `tag_id`
-   Unique pair

List APIs accept `cursor`, `limit`, and filters (`tag`, `folder_id`, `q`,
`status`) for infinite-grid browse.

### Document

-   `id`
-   `workspace_id`
-   `folder_id` nullable
-   `title`
-   `slug`
-   `body_markdown`
-   `status`: `draft`, `published`, `archived`
-   `created_by`
-   `updated_by`
-   `created_at`
-   `updated_at`
-   `current_revision_id` nullable

Use revision checks or optimistic concurrency to prevent silent
overwrites.

### DocumentRevision

-   `id`
-   `document_id`
-   `revision_number`
-   `title`
-   `body_markdown`
-   `created_by`
-   `created_at`
-   optional `change_summary`

### Folder

-   `id`
-   `workspace_id`
-   `parent_id` nullable
-   `name`

### Resource

-   `id`
-   `workspace_id`
-   `name`
-   `type`
-   `config_json` for non-secret configuration
-   encrypted secret data or secret-store reference
-   actor and timestamp fields

### Execution

-   `id`
-   `workspace_id`
-   `document_id`
-   immutable revision reference or document snapshot
-   `trigger_type` (initially `manual`)
-   `status`: `queued`, `running`, `succeeded`, `failed`, `cancelled`
-   `requested_by`
-   `started_at`
-   `finished_at`
-   `duration_ms`
-   `error_summary`
-   `created_at`

Each execution must use the exact validated document snapshot submitted
for execution. Editing the document later must not change an existing
run.

### ExecutionStep

-   `id`
-   `execution_id`
-   `step_key`
-   `action_type`
-   `status`: `pending`, `running`, `succeeded`, `failed`, `skipped`
-   redacted input summary
-   redacted structured output
-   logs or structured log reference
-   timestamps and duration
-   error code/message

### AuditEvent

Record security-sensitive actions, including login events, resource
changes, permission changes, and execution requests. Never record secret
values.

------------------------------------------------------------------------

## 11. Authentication and authorization (Entra ID and RBAC)

### Entra ID / OAuth 2.0

-   **OpenID Connect** on Microsoft identity platform (single-tenant or
    `/common` with tenant lockdown).
-   **Authorization Code + PKCE** from `/login`; callback e.g.
    `/auth/callback/entra`; **session cookie** after ID token validation
    (do not expose refresh tokens to the browser).
-   Confidential client: server-held `ENTRA_CLIENT_SECRET` or certificate;
    document in deployment guide.
-   Environment: `ENTRA_TENANT_ID`, `ENTRA_CLIENT_ID`, `ENTRA_CLIENT_SECRET`
    (or cert paths), `ENTRA_REDIRECT_URI`, `SESSION_SECRET`, optional
    `ENTRA_ALLOWED_TENANTS`.
-   Claims: `oid`/`sub`, `tid`, `email`/`preferred_username`, `name`; validate
    issuer, audience, expiry, nonce/state (CSRF).
-   **JIT provisioning:** first login creates `User` + `ExternalIdentity`.
-   **Local dev:** optional `AUTH_PROVIDER=local` with dev credentials;
    **disabled in production**; Entra is the enterprise production path.

References: [Microsoft OAuth 2.0 authorization code flow](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow).

Post-MVP: map Entra app roles or group IDs → default workspace role on join.

### Roles (workspace-scoped)

| Role | Purpose |
|------|---------|
| `owner` | Full control including workspace delete and ownership transfer |
| `admin` | Members, resources, settings; full document and execution capabilities |
| `editor` | Edit documents, folders, tags; validate and run (non-infra mutating) |
| `runner` | Read documents; run and view executions; no edit or resource admin |
| `viewer` | Read-only; no run |

### Permissions

Implement `ROLE_PERMISSIONS` in server code; APIs call
`requirePermission(ctx, 'documents:write')` (no ad hoc role checks in routes).

| Permission | Description |
|------------|-------------|
| `workspace:read` / `workspace:update` / `workspace:delete` | Workspace metadata |
| `members:read` / `members:manage` | Membership |
| `folders:read` / `folders:write` | Folders |
| `documents:read` / `documents:write` / `documents:delete` | Documents |
| `tags:read` / `tags:write` | Tags |
| `resources:read` / `resources:write` | Integration resources |
| `executions:read` / `executions:run` / `executions:cancel` | Runs (plan/dry-run included in run) |
| `executions:infra` | Mutating Helm and Terraform **apply** / non-dry-run Helm upgrades |

**Default matrix:** `executions:infra` → owner, admin only. `executions:run` →
owner, admin, editor, runner. `documents:write` → owner, admin, editor.
`resources:write` → owner, admin. Viewer: read permissions only.

### Enforcement

-   SvelteKit `handle`: session, `locals.user`, workspace, effective permissions.
-   Authorize before business logic in APIs and form actions; re-check at enqueue.
-   UI hides/disables Skeleton controls without permission (server authoritative).
-   Audit login, role changes, execution requests (see AuditEvent).

------------------------------------------------------------------------

## 12. Execution lifecycle

### Validate (no execution)

1.  User selects **Validate**.
2.  Server authorizes workspace and document access.
3.  Server parses and validates document version and inputs (if supplied).
4.  Server validates resources and permissions via **ActionProviderRegistry**
    for **every** action step.
5.  Return validation result (errors with line/column where feasible). **Do not**
    create `Execution` or enqueue work.

### Run

1.  User selects **Run** (requires `executions:run`; infra steps may require
    `executions:infra`).
2.  Server authorizes the user for the workspace and document.
3.  Server parses and validates the exact document version and input values.
4.  Server validates referenced resources and permissions for all steps.
5.  Server creates an immutable execution snapshot and durable `Execution` record.
6.  Server enqueues the execution and returns its ID.
7.  Worker claims the job and marks it `running`.
8.  Worker executes actions sequentially in document order via the registry.
9.  Each step's state, timestamps, redacted logs, and typed output are persisted.
10. On failure, mark the failing step `failed`, later steps `skipped`, execution
    `failed`.
11. On success, mark the execution `succeeded`.
12. The UI updates through polling initially; SSE can be added later.

Reliability requirements: - Handle duplicate queue delivery idempotently
using execution IDs and state transitions. - Recover jobs after worker
crashes; use leases/heartbeats or a recovery process. - Bound global and
per-workspace concurrency. - Apply execution-wide and per-action
timeouts. - Cancellation should be cooperative and explain when an
external action cannot be recalled after dispatch. - PostgreSQL remains
the durable source of truth. - Use structured logs and correlation IDs.

Retrying an action may repeat external side effects. Make this clear in
the UI and only offer retry where its semantics are understood.

------------------------------------------------------------------------

## 13. Security requirements

Security is a release blocker.

### Execution and authorization

-   Never execute actions in the browser.
-   Never expose credentials to client-side JavaScript.
-   Require authentication and authorization for every document,
    resource, and execution API.
-   Enforce workspace boundaries on every server-side access.
-   Support viewer, runner, editor, admin, and owner roles.
-   Require an explicit Run action.
-   Validate again on the server even if client-side validation
    succeeded.

### SSRF and outbound network controls

REST execution creates server-side request forgery risk.

-   Deny loopback, private, link-local, multicast, and cloud metadata
    addresses by default.
-   Validate DNS resolution and guard against DNS rebinding.
-   Revalidate redirect destinations and block redirects to prohibited
    destinations.
-   Prefer administrator-configured base URLs and relative paths over
    arbitrary URLs.
-   Make private-network access an explicit administrator-controlled
    option with warnings and audit events.
-   Apply outbound timeouts and size limits.
-   Handle IPv4 and IPv6 robustly.

### Web security

-   Secure session handling, appropriate CSRF protection, secure cookie
    flags, and rate limiting.
-   Sanitize Markdown output and disable raw HTML by default.
-   Prevent stored XSS from document content, action outputs, syntax
    highlighting, and logs.
-   Avoid leaking document names, resources, outputs, or logs across
    workspaces.
-   Never log passwords, session tokens, API keys, or authorization
    headers.

### Execution policy

-   No arbitrary shell or user-provided script execution in MVP.
-   Helm and Terraform providers invoke **pinned CLIs** with schema-bound
    arguments only.
-   Enforce action allowlists, document/step/output limits, timeouts,
    and concurrency caps.
-   Require administrator configuration for sensitive integrations.
-   Audit resource changes and execution attempts.

### Infrastructure providers (Phase 5)

-   Worker image pins `helm` and `terraform` versions; no runtime binary
    downloads from document content.
-   Redact kube and TF tokens from logs; audit every infra step.
-   Run confirmation UI must show provider, operation, release/module, plan vs apply.

### OAuth and sessions

-   OAuth state/nonce; SameSite secure cookies; rate limit login.
-   Document single- vs multi-tenant Entra issuer validation and Azure app
    registration.

------------------------------------------------------------------------

## 14. Editor and document management requirements

-   Edit title, metadata, and document body via **Milkdown Crepe**.
-   WYSIWYG Markdown (headings, lists, tables per enabled Crepe features).
-   **Action blocks:** custom nodes with summary chip (type, id); provider UI
    for REST, Kafka, MQTT, Helm, Terraform; infra blocks show plan/apply badge;
    YAML fence fallback when no UI plugin.
-   `getMarkdown()` on save; optional debounced autosave; explicit Save remains.
-   Inline or panel-based validation errors with source locations.
-   Validate must not execute.
-   Run opens a confirmation/input dialog showing the revision and
    action count.
-   Show saved/saving/unsaved/save-failed state.
-   Warn before leaving with unsaved changes.
-   Show action IDs, types, and concise summaries in preview.
-   Preserve Markdown as the source of truth; do not store only a
    proprietary editor representation.
-   Support copy/export of raw Markdown.
-   Make all controls keyboard accessible.

------------------------------------------------------------------------

## 15. Execution UI requirements

Show: - Overall status and duration. - Initiating user and start time. -
Exact document revision/snapshot used. - Ordered steps with statuses. -
Per-step duration and action type. - Redacted input summaries. -
Structured outputs with size limits and copy controls. - Logs and
actionable error details. - Cancel controls when cancellation is
possible. - Retry controls only when safe enough and confirmed by the
user. - A warning that retrying can repeat external side effects.

Start with polling at a sensible interval and stop polling after a
terminal state. Keep the implementation behind a service boundary so SSE
can be added later.

------------------------------------------------------------------------

## 16. Suggested repository layout

Adapt to the existing project instead of moving files blindly.

``` text
src/
  lib/
    components/
      browse/
        InfiniteDocumentGrid.svelte
      documents/
      editor/
        milkdown/
        providers/
      actions/
      executions/
      resources/
      layout/
    server/
      auth/
        entra.ts
        session.ts
        permissions.ts
        authorize.ts
      db/
      repositories/
      services/
      execution/
        parser/
        validation/
        providers/
          registry.ts
          rest/
          kafka/
          mqtt/
          helm/
          terraform/
        queue/
        secrets/
        logging/
    schemas/
      providers/
    types/
  routes/
    auth/
      callback/
        entra/
    (auth)/
      login/
    (app)/
      app/
        documents/
        executions/
        resources/
        settings/
        members/
    api/
worker/
  src/
    index.ts
    worker.ts
    recovery.ts
migrations/
docs/
  architecture/
  providers/
tests/
  unit/
  integration/
  e2e/
AGENTS.md
.cursor/
  rules/
```

Server-only code must remain server-only and must not leak into client
bundles.

------------------------------------------------------------------------

## 17. Testing strategy

### Unit tests

-   Markdown parsing and action extraction.
-   Frontmatter and provider schemas.
-   Unique action IDs.
-   Template reference resolution and type handling.
-   Authorization and resource permission checks.
-   Secret redaction.
-   REST URL validation and SSRF protections.
-   Timeout and response-size limits.
-   Kafka and MQTT serialization.
-   Execution state transitions and idempotency.
-   RBAC permission matrix.
-   Provider registry rejects unknown `action:foo`.

### Integration tests

-   Database migrations and repositories.
-   Queue enqueue/claim/retry/recovery.
-   REST provider against a local mock server.
-   Kafka provider against a local test broker or container.
-   MQTT provider against Mosquitto (or equivalent) in Compose.
-   Worker crash and restart recovery.
-   Workspace isolation and role enforcement.
-   Mock OIDC for Entra login in CI (no production tenant secrets).

### End-to-end tests

-   Create, edit, save, reload, and export a document (Crepe round-trip).
-   Validate an action document and show line-level errors.
-   Execute REST/Kafka/MQTT workflow using output from a previous step.
-   Display failure details and redacted logs.
-   Create a resource and use it without exposing its secret.
-   Viewer cannot run; runner cannot edit documents or manage resources;
    admin can assign roles.
-   Tag CRUD and infinite-grid pagination with filters.
-   Responsive checklist: documents, editor, run flow at ~375px, ~768px,
    ~1280px.
-   Phase 5: Helm dry-run/test cluster; Terraform plan; apply only with policy.
-   Verify Markdown XSS payloads are sanitized.

Never use production credentials in tests.

------------------------------------------------------------------------

## 18. Local development and deployment

-   Provide `.env.example` with Entra, session, and provider variables (no
    real secrets).
-   Provide Docker Compose for the web app, worker, PostgreSQL, and
    selected queue backend.
-   Kafka can be optional for general local startup, with documented
    local/test setup for Kafka provider tests.
-   Include migration and seed commands.
-   Provide health/readiness endpoints for web and worker processes.
-   Support graceful worker shutdown.
-   Include lint, format, typecheck, unit test, integration test, and
    production build scripts.
-   Pin compatible dependency ranges and commit the lockfile.
-   Document database backup/restore and encryption-key backup.
-   Make deployment configurable through environment variables.
-   Do not require managed cloud services for local development.

------------------------------------------------------------------------

## 19. Implementation plan

### Phase 0 --- Inspect and plan

1.  Inspect the target repository's files, package manager, current
    dependencies, conventions, and deployment configuration.
2.  Summarize the existing architecture before making changes.
3.  Check current official SvelteKit and Skeleton v5 documentation.
4.  Write ADRs (database, queue, editor, **entra-rbac**, action providers).
5.  Add **AGENTS.md** and **`.cursor/rules/`** per §21.
6.  Identify conflicts with this specification and propose the smallest safe
    changes.

**Do not overwrite existing working configuration without a reason.**

### Phase 1 --- App shell, auth, and persistence

-   Scaffold SvelteKit + TypeScript + Skeleton v5; responsive app shell.
-   **Entra OIDC** + sessions + RBAC; members admin UI (`members:manage`).
-   PostgreSQL schema: users, external identities, tags, folders, documents.
-   Paginated document APIs for infinite grid; tag filters.
-   Document CRUD, search, revision snapshots; Docker Compose.

### Phase 2 --- Milkdown editor and parser

-   Milkdown Crepe + action-block plugin; remark parity tests.
-   Frontmatter and action-block parsing; validation API with source positions.
-   Do not execute actions yet.

### Phase 3 --- Execution infrastructure

-   **ActionProviderRegistry**; execution records, queue, worker, sequential
    executor, logs, recovery; unknown provider errors.
-   Authorization and immutable execution snapshots.

### Phase 4 --- Integration providers

-   Resource management and encrypted secrets.
-   REST (SSRF protections), Kafka, **MQTT**; Mosquitto in Compose for tests.
-   Typed output references and redaction; `docs/providers/` for rest, kafka, mqtt.

### Phase 5 --- Infrastructure providers and polish

-   **Helm** and **Terraform** providers; Kubernetes/Terraform resources;
    chart/module allowlists; infra confirmation UX and `executions:infra`.
-   Execution history, cancel/retry, audit filters.
-   Accessibility, empty states, editor chrome CSS bridge.
-   Provider docs: `helm.md`, `terraform.md`; deployment and backup guides.

### Risks and mitigations

-   **WYSIWYG vs Markdown first:** `getMarkdown()` + server remark validation.
-   **Crepe bundle size:** lazy-load editor routes.
-   **ProseMirror vs line errors:** mapping layer or dual display.
-   **Helm/Terraform vs no shell:** schema-bound CLI only (§4.2).
-   **Entra tenant modes:** document `ENTRA_ALLOWED_TENANTS` and app registration.

At the end of every phase, run formatting, linting, type checking,
tests, and production build. Fix failures before proceeding.

------------------------------------------------------------------------

## 20. MVP acceptance criteria

The MVP is complete when:

1.  Users sign in via **Entra** (or documented enterprise mode); APIs return
    **403** when permission is missing; workspace data is isolated.
2.  Users can create, edit (Crepe), save, search, tag, filter, export, and
    restore Markdown documents.
3.  Primary flows work at mobile, tablet, and desktop widths without harmful
    horizontal scroll (except intentional table overflow).
4.  Markdown round-trips through Crepe; action blocks remain readable in source.
5.  REST, Kafka, and MQTT blocks validate; unknown providers fail explicitly.
6.  Authorized users can **Run** with validated inputs; **Validate** never enqueues.
7.  Worker runs steps sequentially; outputs flow to later steps.
8.  Credentials live in resources only; never in browser or logs.
9.  Executions and steps have durable status, outputs, and redacted logs.
10. Failed steps skip later steps; worker recovery and idempotency hold.
11. SSRF defaults block loopback, private, link-local, and metadata targets.
12. Tests cover RBAC, registry, providers, Crepe round-trip, tags, pagination.
13. Local start via documented commands and Docker Compose.
14. README and `docs/providers/` cover format, security, Entra setup, limitations.
15. **Phase 5 complete:** Helm dry-run/test cluster and Terraform **plan**;
    **apply** only with workspace policy and `executions:infra`.

------------------------------------------------------------------------

## 21. Development agent compatibility (dev-only)

Purpose: coding agents (Cursor, Claude Code, Codex) can implement DevDocs without
re-deriving architecture. **Not** a product runtime MCP for Validate/Run.

Required repository artifacts (Phase 0):

| Artifact | Role |
|----------|------|
| `AGENTS.md` (repo root) | Stack, spec pointer, commands, security, layout |
| `.cursor/rules/devdocs-core.mdc` | Markdown-first, remark canonical, phased delivery |
| `.cursor/rules/devdocs-ui.mdc` | Skeleton v5 only |
| `.cursor/rules/devdocs-responsive.mdc` | Mobile/tablet/desktop required |
| `.cursor/rules/devdocs-editor.mdc` | Milkdown Crepe, round-trip |
| `.cursor/rules/devdocs-execution.mdc` | Worker, registry, SSRF |
| `.cursor/rules/devdocs-auth.mdc` | Entra OIDC, RBAC helpers |
| `docs/architecture/*.md` | ADRs: editor, grids, tags, action providers, entra-rbac |

------------------------------------------------------------------------

## 22. Instructions for the coding agent

Act as a senior full-stack engineer implementing DevDocs. Read
**AGENTS.md** and **docs/devdocs-product-spec.md** before coding.

1.  **Inspect first.** Report the repository structure, stack, current
    implementation, and constraints before changing files.
2.  **Plan before large changes.** Propose a phased plan and identify
    architecture decisions that need confirmation.
3.  **Work incrementally.** Do not generate the entire app in one giant
    change.
4.  **Keep the project runnable.** At the end of each phase, run
    formatting, lint, typecheck, tests, and production build.
5.  **Use current APIs.** Follow SvelteKit and **Skeleton v5** documentation.
    Do not use Skeleton v2--v4 patterns.
6.  **Validate dependencies.** Before adding a dependency, explain why
    it is needed and check maintenance and compatibility.
7.  **Reuse good existing code.** Do not replace existing architecture
    merely to match the suggested layout.
8.  **Test as you build.** Add tests alongside each feature.
9.  **Treat security as mandatory.** Execution security, authorization,
    secret handling, SSRF prevention, and safe Markdown rendering are
    release blockers.
10. **Do not invent APIs.** Confirm package APIs and version
    compatibility.
11. **Surface conflicts.** If the existing repository conflicts with
    this specification, explain the trade-off and propose the smallest
    safe change.
12. **Do not claim unfinished work is complete.** Clearly report
    implemented functionality, test results, limitations, and next
    steps.

### Expected first response from the coding agent

Before writing code, provide: - A concise repository assessment. - A
list of existing functionality that can be reused. - Any important gaps
or conflicts with this spec. - A proposed implementation sequence. - The
initial dependencies and architecture decisions, with reasons.

Then begin Phase 1 once the plan is clear.

------------------------------------------------------------------------

## 23. Final product definition

DevDocs is a **self-hostable, Markdown-first web platform for
human-readable documents that define and execute operational actions**.

It combines a BookStack-inspired documentation workspace (tags, search,
filters), Milkdown Crepe authoring, Skeleton v5 UI, Entra-backed RBAC, a
modular action-provider registry (REST, Kafka, MQTT, Helm, Terraform), and a
controlled worker runtime with clear execution history.

The goal is not to reproduce every feature of a general workflow engine.
It is to make the document itself the best place to explain, maintain,
validate, and run an operation.
