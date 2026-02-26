# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **genealogy data repository** (not a software project) for digitalizing the Davatz/Davats/Taffattz/Davaz family tree using the [Gramps Project](https://gramps-project.org). Licensed under GPLv3.0.

## Repository Contents

- **davaz.gramps** — Primary data file in Gramps XML format (uncompressed for clearer diffs, no media). This is the source of truth.
- **davaz.ged** — GEDCOM export for PDF generation. Excludes living people and those marked private (`priv="1"`).
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

## ID Collision Management (Merge Conflicts)

Sequential IDs (`I0127`, `E0045`, etc.) are generated top-to-bottom in the XML. Parallel work causes duplicates. To fix:

1. Find the last ID of each type in master (last entry in the XML)
2. Increment from there for new entries in your branch
3. Merge often to minimize adjustment work

Note: ID collisions are mostly cosmetic — duplicates import fine. Handle (UUID) collisions are theoretically possible but treated as impossible.
