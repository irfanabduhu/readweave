---
name: read
description: |
  Distill a single book into a reading guide and slide presentation. Trigger on requests to read, distill, or present a book, or when user provides an epub/PDF/text file and wants to understand or present it. Produces a comprehensive markdown reading guide and an elegant single-file HTML slide presentation with direct excerpts and analytical margin annotations. Invoke with /read.
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
4. Give each agent the **detailed extraction brief** below

**For `.pdf` files:**

First, detect whether the PDF is scanned (image-based) or has extractable text:

```bash
# Test if PDF has extractable text (check first 5 pages)
pdftotext "$PDF_PATH" - 2>/dev/null | head -c 500
```

- **If output is empty or near-empty** (fewer than ~50 characters of real text): the PDF is scanned. Run OCR before proceeding:
  ```bash
  ocrmypdf --skip-text --output-type pdf "$PDF_PATH" "${PDF_PATH%.pdf}-ocr.pdf"
  ```
  Then use the OCR'd file (`*-ocr.pdf`) for all subsequent reading. Inform the user that OCR was performed and how long it took.

- **If output contains readable text**: proceed normally.

After OCR (if needed):
- Read the PDF directly using the Read tool with page ranges
- Launch parallel agents to cover different page ranges
- Give each agent the same extraction brief

**For plain text / `.txt` files:**
- Read the file directly, splitting into logical sections for parallel processing
- Give each agent the same extraction brief

### Critical: Parallel reading is the key performance optimization. Always use multiple agents reading simultaneously rather than sequential reading.

### Extraction Brief (include this in every agent prompt)

Each agent must read its assigned chapters **slowly and completely** — do not skim. For each chapter, extract:

**1. Candidate passages (the most important output)**

Collect **8-15 candidate passages per chapter**. This is deliberately more than you will use — over-extraction is essential. You cannot curate well from a thin pool.

For each candidate passage, capture:
- The **exact text**, preserving the author's wording precisely. Do not paraphrase, compress, or "clean up" the prose.
- **Full paragraphs**, not isolated sentences. A good excerpt is typically 3-8 sentences (60-200 words). If the idea needs two paragraphs to land, capture both. An excerpt that ends mid-thought is worse than one that runs slightly long.
- The **chapter number, chapter title, and section heading** (if any) where the passage appears, so it can be precisely cited later.
- A **one-line note** on why this passage matters — what idea it captures.

**2. What to look for — the seven passage types:**

Hunt specifically for these. Most chapters will contain 2-4 of these types:

- **Thesis crystallizations**: Where the author states a core claim with maximum clarity and force. These are often mid-chapter, NOT in introductions — authors warm up before making their sharpest formulations.
- **Defining passages**: Where a key concept is introduced, named, or given its clearest definition. These anchor the reader's understanding. Capture the full paragraph around the definition, not just the sentence with the term.
- **Pivotal arguments**: Where the author makes a logical move that the rest of the book depends on — an inference, a distinction, a reductio, a thought experiment. These are the structural load-bearing passages.
- **Striking examples and cases**: Where an abstract idea is made concrete through a historical case, an analogy, a narrative, or a thought experiment. Often the most memorable passages in a book. (Kuhn's duck-rabbit, Rawls's veil of ignorance, Arendt's Eichmann — the example *is* the idea.)
- **Rhetorical high points**: Where the author's prose reaches peak intensity — passages that are not just true but beautifully or powerfully said. These often come at chapter endings or in moments of polemic. The prose quality matters because it makes the idea stick.
- **Self-revisions and qualifications**: Where the author complicates, limits, or walks back a previous claim. These are easy to miss but essential — they are where intellectual honesty lives.
- **Provocation and challenge**: Where the author deliberately challenges the reader's assumptions or the received wisdom of their field. Passages that make you stop and argue back.

**3. What NOT to extract:**

- **Throat-clearing**: Introductory paragraphs that preview the chapter without making a claim ("In this chapter, I will argue that..."). Wait for the actual argument.
- **Literature review**: Passages that merely summarize other thinkers' positions without the author's own analytical voice. Exception: when the summary itself reveals the author's interpretive framework.
- **Methodological boilerplate**: Passages about the book's structure, organization, or scope — unless the author's method IS a key idea.
- **Repetition**: If the same idea is stated in three places, capture the strongest formulation and note the others exist.
- **Fragmentary sentences**: Never extract a sentence that begins with "This" or "It" where the referent is in the previous (uncaptured) paragraph. If you must include such a passage, extend the excerpt upward to include the antecedent.

**4. Chapter-level context:**

For each chapter also record:
- The chapter's **central claim** in one sentence
- How it **advances the book's argument** — what does this chapter add that the previous one didn't?
- Any **key terms introduced or redefined** in this chapter
- **Internal cross-references** — does the author refer back to earlier chapters or forward to later ones?

---

## Phase 2: Analyze & Select

This phase is where raw extraction becomes a presentation. Work through it carefully.

### 2a. Map the Argument

Synthesize the findings from all agents into:

1. **Structural outline** of the book's argument — how it builds from premise to conclusion. Map the logical dependencies: which chapters depend on which? What is assumed vs. argued?
2. **The book's arc**: identify the trajectory from introduction through core argument, evidence, implications, to conclusion
3. **Critical perspectives**: note major critiques or responses from the broader intellectual community where relevant

### 2b. Select Excerpts (the critical step)

From the pool of candidates, select **25-35 best excerpts**. This is NOT a simple "pick the best" — selection must be strategic.

**The three-pass selection process:**

**Pass 1 — The non-negotiables.** Identify the passages that you *cannot* leave out — the ones where the core thesis is stated most powerfully, where the defining concept is introduced, where the pivotal argument is made. These are typically 8-12 passages. If you can't find them, your agents missed them — go back and re-read those chapters.

**Pass 2 — The arc builders.** Select passages that fill in the argument's progression. After Pass 1, you have the peaks; now add the connective tissue. Choose passages that show *how* the author gets from one peak to the next. These build the reader's understanding so the big moments land.

**Pass 3 — The texture.** Add passages that provide the distinctive flavor of the author's thinking — the memorable examples, the surprising asides, the rhetorical high points, the honest qualifications. These make the difference between a presentation that is *correct* and one that is *alive*.

**Selection quality tests (apply to every excerpt):**

1. **The idea test**: Can you state what idea this passage captures in one sentence? If you can't, the passage may be atmospheric but not substantive — cut it unless it's a rare rhetorical high point.
2. **The stand-alone test**: Can someone who hasn't read the book understand this passage? If it depends on context from the surrounding pages, either extend the excerpt or cut it.
3. **The substitution test**: Could a different passage from the same book capture the same idea equally well? If yes, you haven't found the *best* formulation yet — go back to the candidate pool.
4. **The redundancy test**: Does this passage capture an idea already represented by another selected excerpt? If so, keep only the stronger one.

**Passage length and integrity:**

- **Minimum**: 40 words / 2 sentences. Anything shorter is a quotation, not an excerpt.
- **Sweet spot**: 80-180 words / 3-8 sentences. Long enough to develop the idea, short enough for a slide.
- **Maximum**: ~250 words. If longer, use `<span class="elide">[…]</span>` to trim non-essential clauses — but never elide the argumentative core.
- **Never truncate mid-thought**. An excerpt must begin and end at natural boundaries — the start/end of a paragraph, or a clear sentence break where no antecedent is left dangling.
- **Preserve the author's exact wording**. Do not modernize spelling, "fix" grammar, or simplify vocabulary. If the author wrote "incommensurable" and not "incompatible," that word choice IS the idea.

### 2c. Verify Excerpts Against Source

After selection, **go back to the source material** and re-read each selected passage in its original context. Check:
- Is the wording exactly correct? (Agents sometimes introduce small errors — missing words, swapped phrases, modernized punctuation.)
- Does the excerpt still work when you read the sentences immediately before and after it? Or did you cut too aggressively?
- Is the chapter/section attribution correct?

Fix any errors before proceeding. An excerpt with a wrong word is worse than no excerpt at all — it attributes something to the author they didn't say.

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
