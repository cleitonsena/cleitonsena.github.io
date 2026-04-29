# cleitonsena.github.io

Public wiki for [Cleiton Sena](https://github.com/cleitonsena) — **AI-Native Revenue Architect (Fractional · B2B SaaS · 🇧🇷 🇪🇸 🇩🇪)**.

Concepts, architecture decisions, and lessons from designing AI agent departments for B2B SaaS revenue teams. [Soilytix](https://soilytix.com) is the live case study. Capacity for 1-2 additional fractional engagements.

**Live:** https://cleitonsena.github.io/

## Stack

- [MkDocs](https://www.mkdocs.org/) + [Material theme](https://squidfunk.github.io/mkdocs-material/)
- Auto-deployed to `gh-pages` branch via GitHub Actions on push to `main`
- Subset of internal `cleitonOS-workspace/wiki/` (Karpathy 2nd brain pattern)

## Local preview

```bash
pip install mkdocs-material
mkdocs serve   # http://localhost:8000
```

## Adding / updating content

1. Edit Markdown files in `docs/`
2. Update navigation in `mkdocs.yml` if a new page is added
3. Push to `main` → GitHub Actions rebuilds + deploys

Internal cross-links use MkDocs-relative paths (e.g. `../concepts/X.md`), not internal `wiki/` references. Verify before committing if migrating from the internal vault.

## What's NOT public here

The internal `cleitonOS-workspace/wiki/` contains Soilytix-confidential material (pipeline, pricing, deal-specific notes, internal credentials). Those stay private. Only evergreen frameworks, public-facing concepts, and build-in-public specs are mirrored here.

## License

Content under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — feel free to share + remix with attribution.
