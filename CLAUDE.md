# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **genealogy data repository** (not a software project) for digitalizing the Davatz/Davats/Taffattz/Davaz family tree using the [Gramps Project](https://gramps-project.org). Licensed under GPLv3.0.

## Repository Contents

- **davaz.gramps** — Primary data file in Gramps XML format (uncompressed for clearer diffs, no media). This is the source of truth.
- **davaz.ged** — GEDCOM export for PDF generation. Excludes living people and those marked private (`priv="1"`).
- **docs/** — Publication-ready documents, not tree data. Currently the trilingual Thomas Davatz portrait (`Thomas-Davatz-DE-EN-PT.pdf`) and its HTML source.
- No source code, build system, tests, or dependencies.

## Data Structure (Gramps XML)

Three main node types, each with `handle` (UUID linking nodes together), `change` (timestamp), and `id` (sequential like I0000, F0000, E0000):

- **`<person>`** — ID prefix `I`, contains name, sex, birth/death refs, occupation
- **`<family>`** — ID prefix `F`, links persons via handles
- **`<event>`** — ID prefix `E`, types include Birth, Death, Military Service, Residence

## Critical Workflow Notes

- **No build commands** — Gramps GUI is used to edit the tree and export XML/GEDCOM
- **Export XML**: Gramps UI > Export > Gramps XML > No compression > `davaz.gramps`
- **Export GEDCOM**: Same flow but exclude living/private people > `davaz.ged`
- **Importing quirk**: Gramps *merges* on import rather than replacing. To import updated XML, start a new empty family tree first (Family Trees > Manage > New).

## docs/ — Document Generation

`docs/thomas-davatz.html` is a standalone HTML document rendered to PDF with WeasyPrint:

```
weasyprint docs/thomas-davatz.html docs/Thomas-Davatz-DE-EN-PT.pdf
```

- Print CSS only (`@page`, A4). Fonts are Source Serif Pro / Source Sans Pro; other fonts change pagination.
- **No photographs of living people in this repository.** The repo is public (`github.com/bcat-eu/davaz`), so the committed edition is text-only. A private edition adds a plate section with gathering photographs; keep those images out of `docs/` and out of the Git history.
- Avoid `::first-letter { float: left }` — WeasyPrint shifts only the second line, so drop caps collide with the first line of text.

**Content caveat:** The subject of that document, the emigrant Thomas Davatz (1815–1888), is **not** in `davaz.gramps`, and his dates are from historical literature rather than verified registers. Do not treat the PDF as a source for the tree; verify against Grisons registers first.

## ID Collision Management (Merge Conflicts)

Sequential IDs (`I0127`, `E0045`, etc.) are generated top-to-bottom in the XML. Parallel work causes duplicates. To fix:

1. Find the last ID of each type in master (last entry in the XML)
2. Increment from there for new entries in your branch
3. Merge often to minimize adjustment work

Note: ID collisions are mostly cosmetic — duplicates import fine. Handle (UUID) collisions are theoretically possible but treated as impossible.
