# ADR: Milkdown Crepe editor

## Status

Accepted (spec v1.1)

## Context

DevDocs needs rich Markdown authoring with first-class executable action blocks while keeping stored Markdown portable.

## Decision

- Use **@milkdown/crepe** in Svelte (client-only mount).
- Core action-block Milkdown plugin serializes to `action:<type>` fences.
- **remark/unified on the server** remains canonical for validate/run.
- Persist `body_markdown` from `getMarkdown()` on save.

## Consequences

- Lazy-load editor routes for bundle size.
- Map validation errors to Markdown lines where possible; document ProseMirror gaps.
- CSS bridge aligns Crepe with Skeleton v5 themes.
