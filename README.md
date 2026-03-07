# Book Club Plugin

A Claude Code plugin that creates book club presentations and reading guides from any book file.

## Installation

Clone this repository:

```bash
git clone https://github.com/irfanabduhu/book-club.git ~/book-club
```

Then launch Claude Code with the `--plugin-dir` flag:

```bash
claude --plugin-dir ~/book-club
```

You can clone it anywhere you like — just point `--plugin-dir` at the directory containing `.claude-plugin/`.

## Usage

Once Claude Code is running with the plugin loaded:

```
/book-club path/to/book.epub
```

## What it produces

1. **`[book-name]-reading-guide.md`** — Comprehensive markdown reading guide with chapter analysis, key quotations, critical perspectives, and discussion questions.

2. **`gist.html`** — Single-file HTML slide presentation with book-page aesthetic, direct excerpts, handwritten-style margin annotations, and keyboard/touch navigation. Works offline, no dependencies.

## Supported formats

- `.epub` (extracted and read chapter-by-chapter)
- `.pdf` (read directly)
- Plain text / `.txt`

## How it works

1. **Extract & Read** — Detects file type, extracts content, and launches parallel agents to read chapters simultaneously
2. **Analyze & Select** — Synthesizes findings into 25-35 best excerpts across the book's arc
3. **Margin Annotations** — Writes analytical margin notes that connect, historicize, and surface hidden implications (never mere summary)
4. **Generate Outputs** — Creates both the markdown reading guide and HTML slide presentation in parallel
5. **Refine** — Audits margin note quality, checks flow, verifies consistency between outputs

## Design

The HTML presentation uses a parchment book-page aesthetic with Cormorant Garamond and EB Garamond fonts, margin notes in Caveat cursive, drop caps, and a responsive layout that hides margins below 900px. The single-file output works offline and can be shared as one `.html` file.
