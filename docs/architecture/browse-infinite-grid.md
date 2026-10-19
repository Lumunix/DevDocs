# ADR: Infinite grid browse

## Status

Accepted (spec v1.1)

## Context

Document and execution lists should scale with cursor pagination and card layouts.

## Decision

- Use **@egjs/svelte-infinitegrid** inside Skeleton cards.
- APIs expose `cursor`, `limit`, and filter query params.
- Load more on `requestAppend`; reflow columns by breakpoint.

## Consequences

- Stable cursor semantics required under tag/status filters (integration tests).
- Mobile: fewer columns; filters in Dialog on narrow viewports.
