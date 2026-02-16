---
name: docs-search
description: Search local documentation collections using full-text search. Use when users ask to search docs, index a docs folder, or check index status.
---

# docs-index CLI

Uses SQLite FTS5 for full-text search over local documentation trees. The database is stored at `<docs_root>/search.db`.

## Installation

The script is bundled at `scripts/docs-index` relative to this skill directory. It requires `uv` (no other dependencies). If `docs-index` is not on PATH, install it:

```bash
SKILL_DIR="$(dirname "$(readlink -f "$0")")"  # only needed in scripts
# Or use the absolute path directly:
ln -sf /sdf/data/lcls/ds/prj/prjcwang31/results/configs/.claude/skills/docs-search/scripts/docs-index ~/.local/bin/docs-index
```

Verify: `docs-index --help`

## Subcommands

```bash
# Index (or re-index) a docs folder
docs-index index <docs_root> [--incremental] [--ext EXT ...]

# Search indexed docs
docs-index search <docs_root> <query> [--limit N]

# Show index stats (file count, size, last indexed)
docs-index info <docs_root>
```

**Defaults:** extensions `md rst txt py`, limit `10`.

## FTS5 query syntax

| Pattern | Example | Meaning |
|---------|---------|---------|
| Simple term | `autograd` | Match token anywhere |
| Phrase | `"loss function"` | Exact phrase |
| Boolean OR | `adam OR sgd` | Either term (AND is implicit default) |
| Prefix | `optim*` | Prefix match |
| Combined | `"learning rate" optimizer OR scheduler` | Phrase + boolean |

## Workflow

1. Run `docs-index info <docs_root>` to check if an index exists.
2. If no index (or stale), run `docs-index index <docs_root> --incremental`.
3. Run `docs-index search <docs_root> "<query>" --limit N` to find relevant docs.
4. Read the returned file paths to answer the user's question.
