---
name: book-club
description: |
  Create book club presentations and reading guides from epub, PDF, or text files. Trigger on requests to create book club presentations, reading session guides, book analysis slides, or when user provides an epub/book file and wants to understand/present it. Produces both a comprehensive markdown reading guide and an elegant single-file HTML slide presentation with book-page aesthetic, direct excerpts, and margin annotations. Invoke with /book-club.
argument-hint: "[path to .epub or book file]"
---

# Book Club Skill

You will create two outputs from a book file:
1. A comprehensive **markdown reading guide** (`[book-name]-reading-guide.md`)
2. An elegant **HTML slide presentation** (`[book-name]-gist.html`)

Follow the five phases below in order. Phases 4a and 4b are independent and can run in parallel.

---

## Phase 1: Extract & Read

### Detect the file type and extract content:

**For `.epub` files:**
1. Create a unique temp directory and unzip: `mktemp -d /tmp/book-extract-XXXXXX` then `unzip book.epub -d $TMPDIR`
2. Read the OPF/NCX files to identify chapter order and HTML file paths
3. Launch **3-5 parallel Explore agents**, each covering a range of chapters (e.g., agent 1: chapters 1-4, agent 2: chapters 5-8, etc.)
4. Each agent should extract from its chapters:
   - Key arguments and claims
   - Memorable, quotable passages (capture exact wording)
   - Chapter themes and how they advance the book's arc
   - Striking metaphors, examples, or logical moves

**For `.pdf` files:**
- Read the PDF directly using the Read tool with page ranges
- Launch parallel agents to cover different page ranges

**For plain text / `.txt` files:**
- Read the file directly, splitting into logical sections for parallel processing

### Critical: Parallel reading is the key performance optimization. Always use multiple agents reading simultaneously rather than sequential reading.

---

## Phase 2: Analyze & Select

Synthesize the findings from all agents into:

1. **Structural outline** of the book's argument — how it builds from premise to conclusion
2. **25-35 best excerpts** that represent the core ideas. Select passages that are:
   - Self-contained (readable without surrounding context)
   - Argumentatively significant (advances or crystallizes a key claim)
   - Memorable (distinctive prose, striking metaphors, powerful logic)
   - Representative of the book's full arc, not just highlights
3. **The book's arc**: identify the trajectory from introduction through core argument, evidence, implications, to conclusion
4. **Critical perspectives**: note major critiques or responses from the broader intellectual community where relevant

---

## Phase 3: Write Margin Annotations

For each selected excerpt, write **2-3 margin notes**. Each note has a **label** and a **body**.

### Labels must be:
- Descriptive concepts, not performative exclamations
- Good: "Pessimistic Induction", "Semantic Rupture", "Immunological", "The Logical Fork", "Bootstrapping Problem"
- Bad: "Key Insight!", "Bombshell!", "Important!", "Mind-blowing!", "Note"

### Note body must be:
- **Analytical, not summary** — NEVER restate what the excerpt says. If your note could serve as a summary of the passage, delete it and write something that adds.
- **Cross-referential** — connect to other parts of the book, other thinkers, broader intellectual traditions
- **Hidden implications** — surface what the reader might miss on first reading
- **Concrete** — use specific examples, historical parallels, named thinkers, real events

### Quality test: Cover the excerpt text. Can someone learn something new from just the margin note? If not, rewrite it.

---

## Phase 4a: Generate Markdown Reading Guide

Create a comprehensive markdown document with:

1. **Title and metadata** — book title, author, year, edition info
2. **Opening quotation** — a signature passage from the book
3. **Overview** — 3-4 paragraphs on the book's significance, influence, and origin story
4. **Table of contents** — linked sections
5. **Core argument walkthrough** — the book's main thesis explained with key quotations, organized thematically (NOT chapter-by-chapter summary, but conceptual sections that trace the argument)
6. **Thematic sections** (8-12) — each section covers a key concept with:
   - Explanatory prose contextualizing the idea
   - 2-4 direct quotations from the book
   - Connections to broader intellectual traditions
7. **Critical perspectives** — 3-5 major critiques from respected scholars, with their arguments fairly presented
8. **Beyond the book** — where the ideas resonate in other fields or contemporary life
9. **Discussion questions** — 6-10 thought-provoking questions for group discussion
10. **Key quotations collection** — 15-20 of the most important passages gathered for reference

Save as `[book-name]-reading-guide.md` in the working directory. Use kebab-case for the filename.

---

## Phase 4b: Generate HTML Slides

1. **Read the template** from [assets/gist-template.html](assets/gist-template.html)
2. **Read the design guide** from [references/design-guide.md](references/design-guide.md) for HTML structure of each slide type
3. **Replace placeholders**:
   - `{{BOOK_TITLE}}` → the book's title (used in both `<title>` and sidebar header)
   - `{{SIDEBAR_SUBTITLE}}` → "Author Name, Year"
4. **Populate slides** inside the `<div class="slides-wrapper">` between the template comments:

### Slide structure:
- **Slide 0 — Title**: `class="slide active no-margin"` — book title, author, year, opening quotation. Include `begin-hint`.
- **Slide 1 — Overview**: `class="slide no-margin"` — the book's core argument. Use a visual diagram (cycle-diagram, concept-cards, or similar) plus brief commentary. Frame what follows: "The following pages present [Author]'s argument *in [their] own words*."
- **Slides 2 through N — Excerpts**: `class="slide"` — direct book excerpts with margin annotations. This is the bulk of the presentation (20-30 slides). Each slide has:
  - `page-chapter` label with chapter number and name
  - `page-divider`
  - One or more `.excerpt` paragraphs (first excerpt on most slides gets `.drop-cap`)
  - `page-source` citation
  - `.margin` div with 2-3 `.margin-note` elements
- **Slide N+1 — Discussion**: `class="slide no-margin"` — 6 thought-provoking questions using `.discussion-q` elements
- **Slide N+2 — Closing**: `class="slide no-margin"` — the book's thesis distilled into a phrase + a final quotation + author/title/year attribution

### Formatting rules:
- Use `<span class="elide">[&hellip;]</span>` for elisions within excerpts
- Use `&mdash;` for em-dashes, `&lsquo;`/`&rsquo;` for single quotes, `&ldquo;`/`&rdquo;` for double quotes
- Use `&middot;` as separator in chapter labels and author lines
- Apply `drop-cap` class to the first `.excerpt` on most excerpt slides
- Use `<em>` for italics within excerpts (book titles, emphasis)
- Each slide needs a descriptive `data-title` attribute for the sidebar TOC

Save as `[book-name]-gist.html` in the working directory. Derive `[book-name]` from the book title: lowercase, kebab-case, drop articles (a, an, the). For example, *The Structure of Scientific Revolutions* becomes `structure-scientific-revolutions-gist.html`.

---

## Phase 5: Refine

Review both outputs:

1. **Flow check**: Read through the slides in order — does the argument build naturally? Are there gaps or redundancies?
2. **Margin note audit**: Verify NO margin note merely restates its excerpt. Every note must add analytical value.
3. **Consistency check**: Both the reading guide and slides should reference the same key excerpts and themes
4. **Technical check**: Verify the HTML is well-formed — all tags closed, all slides have `data-title`, first slide has `class="active"`
5. **Pacing**: Aim for 25-35 total slides. Too few loses depth; too many loses momentum.

---

## Important Notes

- **Direct excerpts only**: The presentation shows the author's actual words, not paraphrase. The margin notes provide the analysis.
- **Single-file HTML**: The output HTML must be completely self-contained. All CSS inline in `<style>`, all JS inline in `<script>`. Only external dependency is Google Fonts CDN.
- **Preserve the aesthetic**: The book-page design (parchment colors, Garamond fonts, Caveat margin notes, drop caps) is integral to the experience. Do not modify the CSS from the template.
- **The margin notes are the value-add**: Anyone can excerpt a book. The margin notes — analytical, cross-referential, concrete — are what make this a book *club* rather than a book *report*.
