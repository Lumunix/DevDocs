# ADR: Tags and search

## Status

Accepted (spec v1.1)

## Context

BookStack-inspired discoverability without adopting BookStack's hierarchy.

## Decision

- Keep **Folder + Document**; add workspace-scoped **Tag** and **DocumentTag**.
- Search title, body, tags, folder path; filter by tag, status, folder, updated range.
- Skeleton Tags Input and filter controls in browse UI.

## Consequences

- Index/search strategy TBD at implementation (DB full-text vs dedicated search).
