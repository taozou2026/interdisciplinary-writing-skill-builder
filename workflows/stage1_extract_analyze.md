# Stage 1+2: Extract & Analyze Exemplar Papers

## Overview

For each PDF in the target exemplar folder, extract text and produce a structural analysis note. The analysis captures what each section and paragraph DOES (its rhetorical function), not what it SAYS (its content).

This stage also produces a tracker CSV (borrowed from LitPilot's Excel tracker pattern) for quick cross-paper comparison.

## Inputs

- PDFs in `exemplars/{stream}/`
- Agent prompt: `agents/structural_analyst.md`
- Stream identifier: `trade-econ` or `ib-strategy`

## Outputs

- One analysis note per paper: `analysis/{stream}/{Author_Year_Journal}.md`
- One tracker CSV: `analysis/{stream}/tracker.csv`

---

## Step 1: Extract Text

For each `.pdf` file in the exemplar folder:

```bash
pdftotext -layout "{pdf_path}" "/tmp/extracted/{filename}.txt"
```

### Quality Check

After extraction, verify the text is readable:

```bash
# Quick check: does page 1 have real text?
pdftotext -f 1 -l 1 "{pdf_path}" - | head -20
```

- If the first page returns readable text → proceed with `pdftotext -layout`
- If very little or garbled text → the PDF may be scanned. Fall back:
  1. Try `pdftotext` without `-layout` flag
  2. If still empty, rasterize key pages: `pdftoppm -jpeg -r 150 -f 1 -l 5 "{pdf_path}" /tmp/pages` then read visually
  3. For fully scanned PDFs, rasterize all pages — but flag this to the user as token-expensive
- For most journal articles (born-digital PDFs from publisher websites), `pdftotext -layout` works well

### Handling Multi-Column Layouts

Many journal articles use two-column layout. `pdftotext -layout` preserves spatial positioning, which means columns may interleave. If this happens:
- Try `pdftotext` without `-layout` (reads left column then right column)
- If neither produces clean output, note this in the analysis and work with what you have

---

## Step 2: Identify Metadata

From the extracted text, identify:

| Field | Source |
|-------|--------|
| **Authors** | First page / header |
| **Year** | First page / DOI |
| **Journal** | Header, footer, DOI, or first-page metadata |
| **Title** | First page (abbreviated in analysis note — enough to identify) |
| **DOI** | Search the text for `doi:` or `DOI` or `https://doi.org/` patterns |
| **Paper type** | Empirical / theoretical / review / mixed |
| **Methodology** | DID, gravity model, panel FE, IV, RDD, event study, case study, etc. |
| **Data type** | Firm-level, country-level, product-level, industry-level, etc. |
| **Identification strategy** | What creates exogenous variation |

---

## Step 3: Structural Analysis

Read the full extracted text and produce an analysis note following the agent prompt in `agents/structural_analyst.md`.

### What to Analyze (9 Dimensions)

The analysis covers **nine dimensions** of the paper's architecture:

1. **Introduction Architecture** — paragraph-by-paragraph functional map
2. **Theory/Conceptual Section** — how the paper bridges from literature to hypothesis/estimating equation
3. **Empirical Strategy Presentation** — how identification, data, and specification are introduced
4. **Results Narration** — how tables are discussed, coefficient narration style, robustness presentation
5. **Discussion/Conclusion Architecture** — what the paper discusses vs. avoids
6. **Citation Practices** — density, placement, how literature is woven into argument
7. **Transition Patterns** — how sections connect, signposting style, voice/person
8. **Distinctive Features** — anything structurally unusual or notably effective
9. **Convention vs. Style Confidence** — per-dimension rating of field convention vs. author idiosyncrasy

### Evidence Provenance on Observations

Borrowed from LitPilot's evidence provenance system: every structural observation in the analysis note must be grounded. For each claim about the paper's structure, indicate WHERE in the paper you observed it:

- Reference by section number or title: `(Section 3.1)` or `(Introduction, para 4)`
- Reference by page number when section structure is unclear: `(p. 7)`
- If a pattern spans multiple locations: `(observed in Sections 4.1, 4.3, and 5.2)`

This makes the analysis notes verifiable — the user can spot-check any claim against the source PDF.

---

## Step 4: Update Tracker CSV

After analyzing each paper, append a row to `analysis/{stream}/tracker.csv`. If the file doesn't exist, create it with the header row first.

### Tracker Schema

```csv
filename,authors,year,journal,paper_type,methodology,data_type,identification,intro_paragraphs,intro_hook_type,has_standalone_theory_section,theory_to_empirics_bridge,num_hypotheses,results_tables_count,results_narration_style,has_separate_discussion,conclusion_paragraphs,citation_density,voice_person,notes
```

| Column | Values |
|--------|--------|
| `filename` | PDF filename |
| `authors` | Last names (e.g., "Amiti, Konings") |
| `year` | Publication year |
| `journal` | Journal abbreviation (JIE, JIBS, etc.) |
| `paper_type` | empirical / theoretical / review |
| `methodology` | DID / panel_FE / gravity / IV / RDD / event_study / other |
| `data_type` | firm / country / product / industry / other |
| `identification` | Brief description of identification strategy |
| `intro_paragraphs` | Number of introduction paragraphs |
| `intro_hook_type` | policy_event / puzzle / theoretical_gap / stylized_fact |
| `has_standalone_theory_section` | yes / no |
| `theory_to_empirics_bridge` | hypotheses / estimating_equation / conceptual / model |
| `num_hypotheses` | Number (0 if none) |
| `results_tables_count` | Number of main results tables |
| `results_narration_style` | column_by_column / variable_by_variable / hypothesis_driven |
| `has_separate_discussion` | yes / no |
| `conclusion_paragraphs` | Number of conclusion paragraphs |
| `citation_density` | low_under30 / medium_30to60 / high_over60 |
| `voice_person` | we / impersonal / mixed |
| `notes` | Free-text notes on distinctive features |

This tracker enables quick pattern detection before reading full analysis notes. For instance, if 5/6 trade econ papers have `has_standalone_theory_section = no`, that's a strong field convention signal.

---

## Processing Order

Process papers **one at a time, sequentially**. For each paper:

1. Extract text
2. Quick quality check
3. Read the full text carefully
4. Write the analysis note following the structural analyst agent prompt
5. Append a row to the tracker CSV
6. Save analysis note to `analysis/{stream}/{Author_Year_Journal}.md`

**Do NOT batch-summarize.** Each paper needs individual close reading.

### Incremental Processing

If the `analysis/{stream}/` folder already contains analysis notes from a previous run:
- Only process PDFs that don't yet have a corresponding analysis note
- Append new rows to the existing tracker CSV
- This supports LitPilot's incremental workflow — add exemplars over time without re-processing

To check which papers still need processing:
```bash
# List PDFs without a corresponding analysis note
for pdf in exemplars/{stream}/*.pdf; do
    base=$(basename "$pdf" .pdf)
    [ ! -f "analysis/{stream}/${base}.md" ] && echo "$base"
done
```

---

## Token Budget Note

Each paper analysis requires reading the full extracted text (typically 8,000-15,000 tokens for a journal article) and producing a detailed analysis (~3,000-5,000 tokens output). For 6+ papers per stream, expect significant token usage. Process one stream at a time.

---

## After This Stage

**STOP and hand control back to the user.** The user must review the analysis notes (Stage 2.5 in the CLAUDE.md pipeline) before synthesis proceeds. Do not automatically continue to Stage 3.

Remind the user:
> "I've completed the structural analysis of [N] papers. The analysis notes are in `analysis/{stream}/`. Please review them — check that the introduction architecture, theory-to-empirics bridge, and results narration patterns look right. Edit anything that seems off. When you're satisfied, ask me to synthesize them into a SKILL.md."
