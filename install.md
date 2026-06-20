# Post-Installation Guide

## First Run

After installing the plugin, run:

```
/setup
```

This will:
1. Check your local environment for required tools (uv, Python, extraction tools)
2. Offer to install missing tools (with your permission)
3. Ask you to confirm extraction tools, reference engines, and Obsidian plugins
4. Write `.kb-config.md` with your selections
5. Create the vault directory skeleton

## Typical Workflow

### Building the knowledge base

```
/setup                          ← one-time initialization
/ingest lecture-01.pdf          ← add a single source
/ingest-batch ./slides/         ← add a directory of sources
/ingest-exam midterm-2024.pdf   ← add exam questions
/lint                           ← verify vault health
```

### Using the knowledge base

```
/q What is the relationship between X and Y?
/q Write an exam answer for: explain concept Z
/q Compare concept A and concept B
/status                         ← check coverage
```

### Maintenance

```
/lint                           ← find issues
/rebuild-index                  ← fix stale index
/compare-extractions file.pdf   ← diagnose extraction quality
```

## Obsidian Setup

For the best experience, install these Obsidian community plugins:

1. **Claudian** — lets you run `/q` directly from Obsidian
2. **Dataview** — creates coverage dashboards from vault frontmatter
3. **Obsidian Git** — auto-checkpoints before large ingests
4. **Smart Connections** — suggests relevant notes when querying

## Configuration

All settings live in `.kb-config.md` at your project root. Re-run `/setup` to reconfigure.

## Troubleshooting

| Problem | Solution |
|---------|----------|
| "kb-config.md not found" | Run `/setup` first |
| Extraction tool not found | Run `/setup` to reinstall, or install manually with `uv tool install` |
| Wikilinks broken after ingest | Run `/lint` then `/rebuild-index` |
| Index out of date | Run `/rebuild-index` |
