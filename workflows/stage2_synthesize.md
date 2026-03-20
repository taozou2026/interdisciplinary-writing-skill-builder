# Stage 3: Synthesize Analysis Notes into SKILL.md

## Overview

Read all analysis notes from `analysis/{stream}/` and synthesize cross-paper patterns into a single SKILL.md file. This stage uses a dedicated synthesiser agent prompt (`agents/cross_paper_synthesiser.md`), following LitPilot's design principle that analysis and synthesis are distinct cognitive tasks requiring distinct prompts.

## Prerequisites

- **Stage 2.5 complete:** The user has reviewed and approved all analysis notes in `analysis/{stream}/`. Do NOT run this stage if the user hasn't confirmed they've reviewed the notes.
- All analysis notes in `analysis/{stream}/` (produced by Stage 1+2)
- Tracker CSV at `analysis/{stream}/tracker.csv`
- Cross-paper synthesiser agent: `agents/cross_paper_synthesiser.md`
- SKILL.md template: `templates/skill_skeleton.md`
- Stream identifier: `trade-econ` or `ib-strategy`

## Process

### Step 1: Read Inputs

1. Read the cross-paper synthesiser agent prompt in full
2. Read the SKILL.md template to understand the target structure
3. Read the tracker CSV for a quantitative overview
4. Read ALL analysis notes in `analysis/{stream}/`

### Step 2: Execute Synthesiser Agent

Follow the synthesiser agent's 11-step process:

1. Read all analysis notes
2. Quantitative pattern scan (tracker CSV)
3. Qualitative pattern extraction (9 dimensions)
4. Convention vs. style classification (≥70% threshold)
5. Generate anti-patterns
6. Generate results narration templates (fixest/modelsummary specific)
7. Build Socratic mode (from Academic Research Skills' Socratic Mentor)
8. Build Revision Coach mode (from Academic Research Skills' Revision Coach)
9. Build Devil's Advocate self-review (from Academic Research Skills' Devil's Advocate)
10. Build Integrity Check (from Academic Research Skills' Integrity Verification)
11. Fill the SKILL.md template

### Step 3: Write Output

Save the completed SKILL.md to `output/academic-writing-{stream}/SKILL.md`.

### Step 4: Generate Synthesis Report

Also save a brief synthesis report to `output/academic-writing-{stream}/synthesis_report.md` documenting:

- How many exemplars were analyzed
- Which journals are represented
- For each dimension: what pattern was extracted, what threshold it met, and how it was classified
- Any dimensions where exemplars significantly disagreed (and how the conflict was resolved)
- Known limitations (e.g., "only 1 paper from GSJ — patterns for that journal are uncertain")

This report is for the user's reference when evaluating or editing the SKILL.md.

## Incremental Synthesis

If this is a re-synthesis (new exemplars added to an existing set):

1. Read the existing SKILL.md in `output/academic-writing-{stream}/SKILL.md`
2. Read ALL analysis notes (old + new)
3. Re-run the full synthesis process
4. Overwrite the existing SKILL.md

Unlike LitPilot's incremental synthesis which only processes new papers, our synthesis always re-reads all notes because the pattern threshold calculations require the full set. However, the analysis notes themselves are not re-generated — only the synthesis is re-run.

## Output

Two files:
- `output/academic-writing-{stream}/SKILL.md` — the final skill file
- `output/academic-writing-{stream}/synthesis_report.md` — documentation of synthesis decisions

## After This Stage

Remind the user:
> "The SKILL.md is ready at `output/academic-writing-{stream}/SKILL.md`. I've also produced a synthesis report documenting the pattern extraction decisions. To install it, copy it to your global skills directory:
>
> ```bash
> mkdir -p ~/.claude/skills/academic-writing-{stream}
> cp output/academic-writing-{stream}/SKILL.md ~/.claude/skills/academic-writing-{stream}/SKILL.md
> ```
>
> This installs it parallel to awesome-econ-ai-stuff in `~/.claude/skills/`.
>
> I recommend testing it on a real writing task before considering it finalized. If anything feels off, the synthesis report shows which patterns drove each rule — you can adjust the analysis notes and re-synthesize."
