---
name: readweave
description: Distill books (EPUB, PDF, TXT), web writings (URLs), and blogs into elegant single-file HTML slide presentations and markdown reading guides. Use this skill for summarizing long-form content, creating study materials, or "weaving" multiple books into a unified intellectual map.
---

# ReadWeave Skill

You are an expert at distilling complex long-form content into elegant, readable summaries. This skill automates the process of extracting key insights and presenting them in a high-quality "parchment" aesthetic.

## Available Workflows

Refer to the following command definitions in the `commands/` directory of this skill's installation path:

- **Single Book Distillation**: Use `commands/read.md`. Supports .epub, .pdf (auto-OCR for scanned), and .txt.
- **Multi-Book Synthesis**: Use `commands/study.md`. Unified maps for multiple book files.
- **Web Article Deep-Dive**: Use `commands/digest.md`. Best for individual URLs or essays.
- **Blog Thread Weaving**: Use `commands/crawl.md`. Groups blog posts into thematic threads.
- **Reading Playlists**: Use `commands/playlist.md`. Curated paths for specific topics.

## Core Mandates

1. **Parallel Extraction**: Always use parallel agents (tools) to read different chapters or URLs simultaneously to minimize latency.
2. **High-Fidelity Excerpts**: Never paraphrase key passages. Extract 60-200 word paragraphs that capture the author's original voice.
3. **Margin Annotations**: Write analytical, contextual notes that explain *why* a passage matters, its historical context, or its connection to other ideas.
4. **Single-File Output**: All HTML presentations must be self-contained (CSS/JS inlined) and use the templates in the `assets/` directory.

## Execution Strategy

When a user asks to "read", "study", or "weave" content:
1. Identify the input type (file path or URL).
2. Load the appropriate logic from the `commands/` markdown file.
3. Follow the multi-phase execution (Extraction -> Selection -> Annotation -> Generation).
4. Save progress to `.readweave/` checkpoints as defined in `references/checkpoints.md`.
