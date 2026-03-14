# Elden Ring 100% Tracker

Single-page web app (GitHub Pages) for tracking Elden Ring 100% completion across devices. Progress syncs via GitHub API to `progress.json`.

## Architecture

- **Single file**: Everything lives in `index.html` (~43KB) — HTML, CSS, JS, and the full `DATA` array
- **No build step**: Push to `main` → GitHub Pages auto-deploys
- **Persistence**: GitHub Contents API (primary), localStorage fallback
- **No dependencies**: Vanilla JS, Google Fonts only external resource

## Key Concepts

### Item IDs & progress.json

Items are identified by composite keys generated at render time:

```
{section-id}__{subsection-name}__{item-index}   (with subsections)
{section-id}__{item-index}                       (without subsections)
```

`progress.json` is a flat `{ "item-id": 1 }` dict. Only checked items appear. **This file must never be disrupted** — it represents live user progress synced from the browser.

### The DATA Array

The `DATA` array in `index.html` defines all sections, subsections, and items. Each item has `text` and `tags` (boss, legendary, key, quest, missable, achievement, build). The render loop builds IDs from this structure.

Subsections can also have a `sub_text` field for descriptive/guide text displayed below the subsection header (used in build guide sections).

## Critical Rules

1. **Never modify `progress.json` directly.** It is written by the app via GitHub API.
2. **When modifying DATA items, preserve existing item order and indices within subsections.** Changing order/removing items shifts IDs, which breaks saved progress. New items should be appended, not inserted.
3. **If items must be reordered or removed**, we need a migration strategy for `progress.json` (remap old IDs → new IDs).
4. **Adding new sections or subsections is safe** — they get new IDs that don't collide.

## Workflow

1. Create a feature branch for changes
2. Make edits to `index.html` (usually modifying the `DATA` array)
3. Render the modified HTML locally to visually confirm (`open index.html` in browser)
4. PR into `main` → auto-publishes to GitHub Pages

## Project Structure

```
index.html       — The entire application
progress.json    — Live progress data (DO NOT edit manually)
CLAUDE.md        — This file
.dev/            — Dev notes (gitignored)
.gitignore       — Excludes .dev/
```

## Tags Reference

Items can have these tags: `boss`, `legendary`, `key` (key item), `quest`, `missable`, `achievement`, `build` (build-related item). Tags affect display styling (color-coded badges).
