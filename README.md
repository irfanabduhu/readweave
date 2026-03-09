# Book Club Plugin

A Claude Code plugin that distills books into elegant presentations and reading guides.

## Prerequisites

For scanned PDFs (image-based), the plugin requires `ocrmypdf` and `poppler` (for `pdftotext`) to automatically detect and OCR them before extraction:

```bash
# macOS
brew install ocrmypdf poppler

# Ubuntu/Debian
sudo apt install ocrmypdf poppler-utils

# Fedora
sudo dnf install ocrmypdf poppler-utils
```

These are only needed if you work with scanned PDFs. Text-based PDFs, EPUBs, and plain text files work without any additional dependencies.

## Installation

### Option 1: Install as a marketplace plugin (permanent)

Register the repo as a marketplace, then install:

```bash
claude plugin marketplace add https://github.com/irfanabduhu/book-club.git
claude plugin install book-club
```

The plugin will be available in all future sessions.

### Option 2: Load per-session

```bash
claude --plugin-dir /path/to/book-club
```

Clone the repo first if using this approach:

```bash
git clone https://github.com/irfanabduhu/book-club.git ~/book-club
claude --plugin-dir ~/book-club
```

## Usage

```
/read path/to/book.epub
```

## What it produces

1. **`[book-name]-reading-guide.md`** — Comprehensive markdown reading guide with chapter analysis, key quotations, critical perspectives, and discussion questions.

2. **`[book-name]-gist.html`** — Single-file HTML slide presentation with book-page aesthetic, direct excerpts, handwritten-style margin annotations, and keyboard/touch navigation. Works offline, no dependencies.

## Supported formats

- `.epub` (extracted and read chapter-by-chapter)
- `.pdf` — text-based (read directly) and scanned/image-based (auto-OCR'd, then read)
- Plain text / `.txt`

## How it works

1. **Extract & Read** — Detects file type, extracts content, and launches parallel agents to read chapters simultaneously
2. **Analyze & Select** — Synthesizes findings into 25-35 best excerpts across the book's arc
3. **Margin Annotations** — Writes analytical margin notes that connect, historicize, and surface hidden implications (never mere summary)
4. **Generate Outputs** — Creates both the markdown reading guide and HTML slide presentation in parallel
5. **Refine** — Audits margin note quality, checks flow, verifies consistency between outputs

## Design

The HTML presentation uses a parchment book-page aesthetic with Cormorant Garamond and EB Garamond fonts, margin notes in Caveat cursive, drop caps, and a responsive layout that hides margins below 900px. The single-file output works offline and can be shared as one `.html` file.
