# Book Club Plugin

A Claude Code plugin that distills books and web writings into elegant presentations and reading guides.

## Skills

| Skill | Scope | Input | Output |
|-------|-------|-------|--------|
| `/read` | Single book | `.epub`, `.pdf`, or `.txt` file | HTML slides + markdown reading guide |
| `/study` | Multiple books | Multiple book files | Unified HTML slides + markdown study guide |
| `/digest` | Single web article | URL to article or essay | HTML slides |
| `/crawl` | Blog or writings page | URL to index/articles page | Unified HTML slides grouped by thematic nets |

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

### From the marketplace (recommended)

Add the marketplace, then install individual skills:

```bash
# Add the marketplace
claude plugin marketplace add irfanabduhu/book-club

# Install skills (pick the ones you want)
claude plugin install read@irfanabduhu
claude plugin install study@irfanabduhu
claude plugin install digest@irfanabduhu
claude plugin install crawl@irfanabduhu
```

Or use the interactive plugin browser:

```bash
claude plugin  # Go to "Discover" tab to browse and install
```

To update plugins when new versions are available:

```bash
claude plugin marketplace update irfanabduhu
```

### Load per-session (alternative)

```bash
git clone https://github.com/irfanabduhu/book-club.git ~/book-club
claude --plugin-dir ~/book-club
```

## Usage

```bash
# Distill a single book
/read path/to/book.epub

# Study multiple books together
/study path/to/book1.pdf path/to/book2.epub path/to/book3.txt

# Deep dive into a web article
/digest https://paulgraham.com/greatwork.html

# Crawl a blog and weave articles into thematic nets
/crawl https://paulgraham.com/articles.html
```

## How it works

### /read — Single book distillation
1. **Extract & Read** — Detects file type (auto-OCRs scanned PDFs), launches parallel agents to read chapters simultaneously
2. **Analyze & Select** — Synthesizes findings into 25-35 best excerpts across the book's arc
3. **Margin Annotations** — Writes analytical margin notes that connect, historicize, and surface hidden implications
4. **Generate Outputs** — Creates both the markdown reading guide and HTML slide presentation in parallel
5. **Refine** — Audits margin note quality, checks flow, verifies consistency between outputs

### /study — Multi-book intellectual tracing
1. **Extract & Read All Books** — Parallel extraction across all books simultaneously
2. **Curate & Analyze** — Builds argument maps per book, selects 15-25 excerpts each
3. **Cross-Book Analysis** — Identifies conceptual threads, determines reading order, plans narrative arc
4. **Margin Annotations** — Writes cross-book annotations showing how ideas evolve
5. **Generate Outputs** — Creates unified study guide and slide presentation (chunked generation for 4+ books)
6. **Refine** — Audits cross-references, checks balance, verifies arc coherence

### /digest — Single article deep dive
1. **Fetch & Extract** — Retrieves article, extracts 8-20 candidate passages
2. **Analyze & Select** — Maps the argument, selects 8-15 excerpts
3. **Margin Annotations** — Writes contextual, analytical margin notes
4. **Generate HTML Slides** — Produces a single-file presentation

### /crawl — Blog weaving
1. **Discover** — Fetches index page, extracts all article links and metadata
2. **Map & Group** — Classifies articles into 3-7 thematic nets from titles and descriptions
3. **Fetch & Extract** — Parallel agents fetch full articles per net, extract candidate passages
4. **Curate & Weave** — Selects excerpts per net, orders by conceptual progression, plans cross-net connections
5. **Margin Annotations** — Writes cross-article and cross-net annotations
6. **Generate HTML Slides** — Produces a unified presentation with net transitions and evolution slides

## Supported formats

- `.epub` (extracted and read chapter-by-chapter)
- `.pdf` — text-based (read directly) and scanned/image-based (auto-OCR'd, then read)
- Plain text / `.txt`
- Web URLs (articles, blog posts, writings pages)

## Design

All HTML presentations use a parchment book-page aesthetic with Cormorant Garamond and EB Garamond fonts, margin notes in Caveat cursive, drop caps, and a responsive layout that hides margins below 900px. Every output is a single self-contained `.html` file that works offline.
