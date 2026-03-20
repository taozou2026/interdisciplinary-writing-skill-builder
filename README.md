# Academic Writing Skill Builder

## Purpose

This project makes the unwritten conventions of academic fields **explicit, comparable, and teachable**.

Every academic field has its own structural DNA — how an introduction is built, whether hypotheses are expected, how results are narrated, what a conclusion must contain. These conventions are rarely documented. Researchers absorb them over years of reading, writing, and painful R&R experiences. They live in people's heads, not in any file.

This matters most for **interdisciplinary research**. When a phenomenon sits at the intersection of multiple fields, the researcher must navigate multiple sets of conventions simultaneously. The same data and findings require structurally different papers depending on the target community. Without making these conventions explicit, interdisciplinary work becomes unnecessarily difficult, and the bridges between fields remain unbuilt.

This project solves that problem. It processes exemplar journal articles from any field to extract structural and stylistic patterns, then synthesizes those patterns into field-specific SKILL.md files for use with AI coding assistants (Claude Code, Cursor, etc.). Run it once per field, and you have a permanent, opinionated writing rulebook. Run it across multiple fields, and you get something more powerful: **a legible map of where fields converge and diverge** — enabling researchers to translate their work across communities, hand co-authors or PhD students an explicit guide to "how the other side thinks," and contribute to the growing calls for interdisciplinary integration.

**Inspired by two upstream workflows:**
- [Academic Research Skills](https://github.com/Imbad0202/academic-research-skills) (Cheng-I Wu) — multi-agent pipeline for research, writing, integrity verification, peer review, and Socratic guidance
- [LitPilot](https://github.com/JCL988/LitPilot) (Juncheng Lyu) — structured agent prompts for PDF literature processing with evidence provenance, thesis-aligned extraction, Excel tracking, and incremental synthesis

This project borrows specific design patterns from each (documented below) and adapts them for building field-calibrated writing skills.

### How This Project Differs

| | Academic Research Skills | LitPilot | **Skill Builder (this project)** |
|---|---|---|---|
| **Purpose** | Write a paper with AI assistance | Organize literature for a paper | **Build a field-calibrated writing rulebook** |
| **Input** | Research question + data | PDFs you'll cite | **PDFs you want to write like** |
| **Output** | Drafted paper + review reports | Structured reading notes + tracker | **SKILL.md writing rulebook per field** |
| **Scope** | Full pipeline (research → publication) | Pre-writing (reading → notes) | **Meta-tool (exemplars → writing rules)** |
| **Field specificity** | Generic academic | User-customized sections | **Field-calibrated from real published papers** |
| **Cross-field comparison** | No | No | **Yes — the core value proposition** |
| **When you use it** | Every time you write | Every time you read | **Once per field, then reuse forever** |
| **Multi-field awareness** | Single-field workflow | Single-field workflow | **Designed for researchers working across fields** |

The key difference: Academic Research Skills and LitPilot are tools you use directly to do research and write papers. This project is a **meta-tool** — it reads exemplar papers and produces a SKILL.md file that then guides future writing. More importantly, it's the only one designed for **cross-field comparison** — producing multiple SKILL.md files that make visible exactly where fields diverge in their conventions, enabling researchers to navigate interdisciplinary work deliberately rather than intuitively.

---

## Quick Start Example

Suppose you work across two fields that study similar questions but write papers very differently:

**1. Create exemplar folders** — one per field you want to target:
```bash
mkdir -p exemplars/field-a exemplars/field-b
```

**2. Select exemplar papers.** Choose 3-5 published papers per field from your target journals. Good exemplars are:

- **Award-winning or most-cited papers** from the field's top journals — these represent what the field considers its best work, and their structural patterns are likely to be field-defining rather than idiosyncratic
- **Recent publications (last 3-5 years)** from the same journals — conventions evolve, and current papers reflect what reviewers expect today
- **Empirical papers with a structure close to what you plan to write** — if you're writing a firm-level empirical paper, choose firm-level empirical exemplars rather than theoretical or review papers

Drop the PDFs into the appropriate folder and name them descriptively: `Author_Year_Journal.pdf`

**3. Run the analysis** (in Claude Code):
```
Analyze all exemplar papers in exemplars/field-a/ following the workflow in workflows/stage1_extract_analyze.md
```

**4. Review the analysis notes** — check that the structural patterns look right, edit anything off.

**5. Synthesize into a SKILL.md:**
```
Synthesize all analysis notes in analysis/field-a/ into a SKILL.md following workflows/stage2_synthesize.md
```

**6. Install and use:**
```bash
mkdir -p ~/.claude/skills/academic-writing-field-a
cp output/academic-writing-field-a/SKILL.md ~/.claude/skills/academic-writing-field-a/SKILL.md
```

Repeat for each field. Once you have two or more SKILL.md files, the cross-field differences become immediately visible — giving you a map of how to translate your work between audiences.

---

## What This Does

```
Stage 1:   Extract text from exemplar PDFs (pdftotext -layout)
Stage 2:   Analyze each paper for structural DNA (one analysis note + tracker row per paper)
Stage 2.5: USER REVIEW — check and correct analysis notes (MANDATORY)
Stage 3:   Synthesize cross-paper patterns into SKILL.md (via dedicated synthesiser agent)
Stage 4:   USER REVIEW — test the SKILL.md on real writing tasks, iterate
```

Each analysis note captures **9 dimensions** of a paper's architecture:
1. Introduction architecture (paragraph-by-paragraph functional map)
2. Theory / conceptual framework structure
3. Empirical strategy presentation
4. Results narration patterns
5. Discussion / conclusion architecture
6. Citation practices
7. Transition and signposting patterns
8. Distinctive features
9. Convention vs. style confidence rating

The synthesis step extracts **cross-paper patterns** and encodes them as opinionated writing rules — with a ≥70% threshold to distinguish genuine field conventions from individual author style.

---

## What This Does NOT Do

- **Does not write papers for you** — it produces a SKILL.md that guides AI-assisted writing
- **Does not discover or retrieve papers** — you bring your own exemplar PDFs
- **Does not replace reading papers** — you review every analysis note before synthesis
- **Does not produce a single "universal" academic writing guide** — it produces field-specific rules that may contradict each other across fields (and that's the point)

---

## Design Patterns Borrowed

### From Academic Research Skills

| Pattern | How We Adapt It |
|---------|-----------------|
| **Socratic Mentor** | `socratic` mode in SKILL.md — guided questioning to build argument architecture before drafting |
| **Argument Builder** | Phase 2 of writing pipeline — every claim maps to a specific result or source |
| **Intake Agent** | SKILL.md reads project context (CLAUDE.md, scripts) before writing |
| **Structure Architect** | Section plan with field-specific paragraph counts and function assignments |
| **Revision Coach** | `revision-coach` mode — parses reviewer comments into classified roadmap with status tracking |
| **Devil's Advocate** | Self-review after drafting — attacks the draft's 3 weakest arguments |
| **Integrity Verification** | Draft-vs-output consistency check — every number in prose must match actual data |
| **Adaptive Checkpoints** | Mandatory confirmation after argument architecture and after each section draft |
| **Material Passport** | Records what user brings (draft? tables? reviewer comments?) to enable mid-pipeline entry |

### From LitPilot

| Pattern | How We Adapt It |
|---------|-----------------|
| **Structured Agent Prompts (BMAD)** | Every exemplar analyzed against identical 9-dimension schema |
| **Evidence Provenance** | Every structural observation grounded with section/page reference |
| **Dedicated Synthesiser** | Separate agent for cross-paper synthesis (distinct from per-paper analysis) |
| **CSV Tracker** | One row per paper with filterable columns for quick cross-paper comparison |
| **Incremental Processing** | Add new exemplars without re-analyzing existing ones |
| **You Supervise, AI Assists** | Mandatory human review gate before synthesis |

---

## How to Use

### Step 1 — Configure your fields

Create one folder per target field under `exemplars/`:
```bash
mkdir -p exemplars/{your-field-name}
```

Name files descriptively: `Author_Year_Journal.pdf`

**Selecting good exemplars:**
- Start with **award-winning or most-cited papers** from the field's top journals — these define field conventions
- Add **recent publications (last 3-5 years)** to capture current expectations
- Prioritize papers **structurally similar to what you plan to write** (e.g., empirical with empirical, review with review)
- Aim for **3+ papers per field** — more exemplars = more robust pattern detection, and the ≥70% threshold becomes more meaningful with larger samples

### Step 2 — Run Stage 1+2: Extract & Analyze

Tell Claude Code:
```
Analyze all exemplar papers in exemplars/{your-field}/ following the workflow in workflows/stage1_extract_analyze.md
```

This produces:
- One analysis note per paper in `analysis/{your-field}/`
- A tracker CSV at `analysis/{your-field}/tracker.csv`

### Step 2.5 — Review analysis notes (MANDATORY)

Open the notes and check:
- Did it correctly identify the introduction architecture?
- Did it capture the theory-to-empirics bridge accurately?
- Are the results narration patterns right?
- Does the convention vs. style confidence rating seem reasonable?

**Do not skip this step.** Synthesis quality depends entirely on analysis quality.

### Step 3 — Synthesize into SKILL.md

Tell Claude Code:
```
Synthesize all analysis notes in analysis/{your-field}/ into a SKILL.md following workflows/stage2_synthesize.md
```

### Step 3.5 — Add more exemplars incrementally (optional)

Drop new PDFs → analyze only the new ones → review → re-synthesize. Existing notes are preserved.

### Step 4 — Install the skill

```bash
mkdir -p ~/.claude/skills/academic-writing-{your-field}
cp output/academic-writing-{your-field}/SKILL.md ~/.claude/skills/academic-writing-{your-field}/SKILL.md
```

### Step 5 — Test on real work

Use the skill on an actual writing task. If the output doesn't match field conventions, return to Stage 2.5.

---

## Directory Structure

```
skill-builder/
├── CLAUDE.md                              # This file
├── workflows/
│   ├── stage1_extract_analyze.md          # Per-paper extraction + structural analysis
│   └── stage2_synthesize.md               # Cross-paper synthesis into SKILL.md
├── agents/
│   ├── structural_analyst.md              # Agent prompt: analyze one exemplar paper
│   └── cross_paper_synthesiser.md         # Agent prompt: synthesize patterns across papers
├── templates/
│   └── skill_skeleton.md                  # SKILL.md template filled by synthesis
├── exemplars/                             # Drop PDFs here — one folder per field
│   ├── {field-1}/
│   ├── {field-2}/
│   └── {field-n}/
├── analysis/                              # Generated analysis notes + tracker.csv
│   ├── {field-1}/
│   ├── {field-2}/
│   └── {field-n}/
└── output/                                # Final SKILL.md files
    ├── academic-writing-{field-1}/
    ├── academic-writing-{field-2}/
    └── academic-writing-{field-n}/
```

---

## Key Design Decisions

1. **Field-specific, not field-agnostic** — each SKILL.md encodes opinionated rules for a specific set of target journals. A single "academic writing" skill would constantly hedge between contradictory conventions. Separate files per field allow each to be direct and actionable.

2. **Cross-field comparison as the core value** — the project's deepest contribution is not any single SKILL.md, but the ability to diff multiple SKILL.md files across fields. When two or more field skills exist, the divergences become a legible map for interdisciplinary work.

3. **Extensible to any field** — the workflow is field-agnostic by design. To add a new field, create `exemplars/{new-field}/`, drop in PDFs from target journals, and run the same pipeline. The cross-field comparison becomes richer with every field added.

4. **Convention vs. style distinction** — every pattern in the analysis is rated for whether it reflects field convention or author idiosyncrasy. Only patterns appearing in ≥70% of exemplars become rules in the SKILL.md. This prevents one author's quirk from becoming a writing rule.

5. **PDF text extraction via `pdftotext -layout`** — not markdown conversion, not rasterization. Journal articles are text-heavy born-digital PDFs; layout-preserving text extraction is the best cost/quality tradeoff.

6. **Argument architecture before prose** — the writing pipeline forces argument structuring as a mandatory step before any drafting. Borrowed from Academic Research Skills' Argument Builder + Structure Architect pattern.

7. **Literature processing deferred** — a hook exists in the SKILL.md for LitPilot-style PDF analysis (processing papers you'll cite into structured reading notes), but implementation is deferred. The evidence provenance rules are documented for future use.

8. **Incremental exemplar addition** — new exemplars can be added without re-processing existing ones. Borrowed from LitPilot's incremental synthesis pattern.

---

## Vision: Bridging Fields Through Explicit Convention

The conventions of academic fields are invisible walls. A researcher who has internalized one field's conventions will unconsciously write a paper that reviewers in another field find structurally wrong — not because the ideas are bad, but because the packaging doesn't match expectations. These walls slow down interdisciplinary work, frustrate co-authorship across fields, and make it harder for insights to travel between communities.

This project makes the walls visible. Once you can see exactly where two fields diverge — in hypothesis conventions, in results narration style, in what a conclusion must contain — you can make deliberate choices about which conventions to follow, which to break, and how to translate your work for different audiences.

The long-term vision extends beyond any two fields. Researchers, practitioners, and policymakers studying overlapping questions from different traditions often struggle to hear each other. A tool that makes field conventions explicit and comparable can help these communities communicate — not by homogenizing their standards, but by making the translation legible.

---

## Requirements

- **Claude Code** (or any AI coding assistant that reads CLAUDE.md and markdown agent prompts)
- **pdftotext** (from poppler-utils) for PDF extraction
- Exemplar PDFs from your target journals

---

## License

[To be determined]

---

## Acknowledgments

This project builds on design patterns from:
- [Academic Research Skills](https://github.com/Imbad0202/academic-research-skills) by Cheng-I Wu (CC BY-NC 4.0)
- [LitPilot](https://github.com/JCL988/LitPilot) by JCL988 (MIT License)
