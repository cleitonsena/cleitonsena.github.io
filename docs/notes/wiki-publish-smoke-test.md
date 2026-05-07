---
title: Wiki Publish Smoke Test
updated: 2026-05-07
sources:
  - https://github.com/cleitonsena/cleitonsena.github.io
tags: [test, wiki, n8n, automation]
---

# Wiki Publish Smoke Test

Synthetic test entry to validate the n8n → GitHub Contents API → MkDocs publishing path.

## Why it matters

Knowledge notes used to require a manual git commit per entry. This test verifies the n8n Publisher can write to `docs/notes/` and trigger the MkDocs build via GitHub Pages without human intervention.

## The principle

A Posts Log row with `Channel=Wiki` flows through the Publisher's `Post Wiki` Code node. The node base64-encodes the body, calls the GitHub Contents API (`PUT /repos/.../contents/{path}`), and MkDocs Material renders it on the next deploy.

## Tradeoffs / when NOT to use

- Wiki publishing is async (MkDocs build delay: 1-3 min on GitHub Pages).
- Slug collisions overwrite previous files. Slug is derived from the title.
- GitHub token currently hardcoded in n8n Code node. Rotate if leaked.

## Connections

- [[wiki/tools/n8n]]
- [[wiki/concepts/wiki-as-knowledge-asset]]

## Sources

1. https://github.com/cleitonsena/cleitonsena.github.io
2. https://docs.github.com/en/rest/repos/contents
