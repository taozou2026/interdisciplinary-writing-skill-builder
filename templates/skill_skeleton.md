# Academic Writing: {STREAM_DISPLAY_NAME}

## Metadata

- **Target Journals:** {JOURNAL_LIST}
- **Paper Type:** Empirical
- **Generated From:** {N} exemplar papers analyzed for structural patterns
- **Stream:** {STREAM_ID}

## Trigger Keywords

academic paper, journal article, draft paper, write paper, write introduction, write results, structure paper, paper outline, argument structure, {STREAM_SPECIFIC_TRIGGERS}

---

## Mode Selection

### write (default)
User wants to draft a paper or section. Activate the full writing pipeline below.

### socratic
User wants guided help structuring their argument before drafting. Activate the Socratic Argument Builder below. This mode is preferred when the user seems uncertain about their contribution, structure, or approach.

**Intent signals for socratic mode** (borrowed from Academic Research Skills' intent-matching pattern):
- User says "help me think through..." / "I'm not sure how to frame..." / "guide me"
- User provides raw results but no clear argument structure
- User asks "where do I start?" or "how should I structure this?"
- When in doubt between `write` and `socratic`, default to `socratic` — guiding first is safer than drafting blind.

### outline
User wants a structured outline without prose. Produce a detailed section plan following the Field Conventions below, including paragraph counts and function assignments.

### narrate-results
User provides regression output (fixest/modelsummary tables) and wants field-appropriate prose narration. Follow the Results Narration rules below.

### revision-coach
User has received reviewer comments and needs a structured revision plan. Activate the Revision Coach below.

### revise
User has a draft and wants revision guidance. Evaluate against the field conventions below and suggest specific structural improvements.

### lit-process [NOT IMPLEMENTED — HOOK ONLY]
Reserved for future implementation. Will process uploaded PDFs into structured reading notes mapped to the paper's argument structure, with evidence provenance (page binding, source attribution, uncertainty flagging — adapted from LitPilot).

When a user requests this mode, respond:
> "Literature processing mode is not yet implemented in this skill. You can use the [LitPilot workflow](https://github.com/JCL988/LitPilot) as a standalone tool, or I can help you manually take structured notes on specific papers following evidence provenance principles. Which would you prefer?"

---

## Writing Pipeline

When a user asks to write a paper or section, follow this sequence. This pipeline is adapted from Academic Research Skills' multi-agent architecture (Intake → Structure Architect → Argument Builder → Draft Writer → Integrity Verification → Devil's Advocate) but flattened into a single-agent pipeline with mandatory checkpoints.

### Phase 1: Intake (from Academic Research Skills' Intake Agent)

Before writing anything, gather the following. If the user provides a CLAUDE.md or project context referencing existing R scripts, read them to understand the empirical setup.

**Required information:**
1. **Research question** — what is the paper asking?
2. **Main finding** — what is the headline result?
3. **Contribution claim** — why does this matter? what's new?
4. **Identification strategy** — what creates causal/exogenous variation?
5. **Data** — what dataset, what level (firm/product/country), what period?
6. **Target journal** — confirm from: {JOURNAL_LIST}
7. **Current stage** — what does the user already have?

**Material Passport** (from Academic Research Skills' Material Passport pattern):
Record what the user brings to enable mid-pipeline entry:

| Material | Status | Notes |
|----------|--------|-------|
| Research question | [clear / vague / missing] | |
| Regression results | [complete / partial / none] | [which R scripts, which tables] |
| Existing draft | [full / partial / none] | [which sections exist] |
| Literature notes | [organized / scattered / none] | |
| Reviewer comments | [received / none] | [for revision-coach mode] |

If the user has complete results but no draft → start at Phase 2.
If the user has a partial draft → assess what's missing and start at the relevant section.
If the user has reviewer comments → switch to `revision-coach` mode.

### Phase 2: Argument Architecture (from Academic Research Skills' Argument Builder + Structure Architect)

Build the paper's skeleton before drafting any prose. This phase is **MANDATORY** — do not skip to drafting.

1. **Contribution statement** (1-2 sentences) — the "so what" of the paper

2. **Claim-evidence chain** — for each major claim, map it to specific evidence:

| Claim | Evidence Source | Table/Column | Strength |
|-------|----------------|--------------|----------|
| [main claim] | [regression result] | [Table X, Col Y] | [direct / suggestive / indirect] |
| [mechanism claim] | [channel test] | [Table X, Col Y] | |
| [robustness claim] | [alternative spec] | [Table X, Col Y] | |

3. **Theoretical mechanism** — the logic connecting X to Y
{THEORY_EMPIRICS_BRIDGE_CONTENT}

4. **Section plan** — ordered list of sections with paragraph counts and function assignments:

{SECTION_PLAN_TEMPLATE}

**CHECKPOINT: Present the argument architecture to the user for approval before proceeding to Phase 3.**

### Phase 3: Section-by-Section Drafting

Draft sections in this order:
1. **Empirical strategy** — most constrained section; gets the technical foundation right
2. **Results** — narrate existing tables using the field-specific patterns below
3. **Theory / conceptual framework** — connect literature to the empirical approach
4. **Introduction** — now that you know what the paper does, write the opening
5. **Discussion / conclusion** — reflect on what it all means
6. **Abstract** — write last, after the full paper exists

This order is deliberate — it prevents the common failure mode of writing a beautiful introduction that promises something the empirics can't deliver.

**Section-level checkpoints:** After drafting each section, briefly confirm with the user before moving to the next. Use SLIM checkpoints for straightforward sections (empirical strategy, results) and FULL checkpoints for interpretive sections (introduction, discussion).

### Phase 4: Integrity Check (from Academic Research Skills' Integrity Verification Agent)

After all sections are drafted, run a consistency check:

- [ ] Every number in prose matches a specific cell in a regression table or summary statistics
- [ ] Every significance claim matches the actual stars/p-values in the table
- [ ] Every "we find" statement is traceable to a specific model specification (Table, Column)
- [ ] No claims are made without empirical support
- [ ] Coefficient signs described in text match the tables
- [ ] Sample sizes mentioned in text match the data description
- [ ] Variable names are consistent between text, tables, and equations

**Anti-hallucination rule** (from Academic Research Skills v2.7): Do NOT verify numbers from memory or inference. If a number appears in the prose, trace it to a specific table/output. If the source table is not available, flag it as unverified.

Report any discrepancies to the user before finalizing.

### Phase 5: Devil's Advocate Self-Review (from Academic Research Skills' Devil's Advocate Agent)

After the integrity check, attack the draft:

1. Identify the paper's **3 weakest arguments** — where is the logic thinnest? where would a reviewer push hardest?
2. Construct the **strongest possible counterargument** for each
3. Assess whether the draft **preemptively addresses** each counterargument
4. If not, suggest specific additions (a sentence, a paragraph, a robustness check reference)
5. Check for **logical gaps** in the claim-evidence chain — are there claims that don't connect to evidence?

Present findings to the user. This is not about making the paper perfect — it's about identifying vulnerabilities before a reviewer does.

---

## Socratic Argument Builder (from Academic Research Skills' Socratic Mentor)

When `socratic` mode is activated, guide the user through structured questions to build their argument architecture. Do NOT draft prose in this mode — only help the user think through their argument.

### Question Sequence

**Round 1 — Research Question**
- What phenomenon are you trying to explain?
- What specific question are you asking?
- Is this a "what happens" question or a "why does it happen" question?

**Round 2 — Contribution**
- What do we already know about this from existing literature?
- What DON'T we know? (This is your gap.)
- Why does filling this gap matter? (For theory? For policy? For practice?)
- Can you state your contribution in one sentence?

**Round 3 — Mechanism**
- What is the theoretical logic connecting your X to your Y?
- What are the assumptions behind this logic?
- Are there competing mechanisms that could produce the same outcome?

**Round 4 — Identification**
- What creates exogenous variation in your setting?
- What are the threats to identification?
- How do you address each threat?

**Round 5 — Evidence Mapping**
- What did you find? (headline result)
- Which results support your main claim?
- Which results test the mechanism?
- Are there results that contradict or complicate your story?

**Round 6 — Architecture**
- Given the above, here's a proposed section plan: [generate based on field conventions]
- Does this structure tell the story you want to tell?
- Is anything missing?

### Convergence Criteria (from Academic Research Skills)

End Socratic mode when ANY of these conditions are met:
1. The user has a clear, one-sentence contribution statement
2. The claim-evidence chain is complete (every major claim mapped to evidence)
3. The section plan is approved by the user
4. The user explicitly says "let's start writing" or equivalent

When converging, summarize the argument architecture and offer to switch to `write` mode.

---

## Revision Coach (from Academic Research Skills' Revision Coach Agent)

When `revision-coach` mode is activated, help the user systematically process reviewer comments.

### Step 1: Parse Comments

Accept pasted reviewer comments (often unstructured, sometimes hostile). Parse into individual actionable items.

### Step 2: Classify Each Item

| # | Reviewer | Comment Summary | Type | Section Affected |
|---|----------|----------------|------|-----------------|
| 1 | R1 | | [MAJOR / MINOR / EDITORIAL / CLARIFICATION / SCOPE] | [Section X] |
| 2 | R1 | | | |
| ... | | | | |

Type definitions:
- **MAJOR** — requires new analysis, significant rewriting, or changes the paper's argument
- **MINOR** — requires modest revision but doesn't change the core argument
- **EDITORIAL** — language, formatting, typos
- **CLARIFICATION** — reviewer misunderstood something; needs clearer writing
- **SCOPE** — reviewer wants the paper to be a different paper; may need to push back

### Step 3: Response Strategy

For each item, propose a strategy:

| # | Strategy | Rationale |
|---|----------|-----------|
| 1 | [ACCEPT / PARTIALLY_ACCEPT / PUSH_BACK / DEFER] | [why this strategy] |

- **ACCEPT** — the reviewer is right; make the change
- **PARTIALLY_ACCEPT** — the reviewer has a point but the suggested fix isn't right; propose an alternative
- **PUSH_BACK** — the reviewer is wrong or the request is out of scope; explain why respectfully
- **DEFER** — acknowledge as valid but outside the scope of this revision; note for future work

### Step 4: Revision Roadmap

Generate a prioritized task list with status tracking:

| # | Task | Priority | Status | Section |
|---|------|----------|--------|---------|
| 1 | | [HIGH / MEDIUM / LOW] | [TODO] | |

### Step 5: Response Letter Structure

Outline the response-to-reviewers letter structure:
- Thank editor and reviewers
- Summary of major changes
- Point-by-point responses (Reviewer 1, then R2, then R3)
- Each response: reviewer comment → our response → what changed in the manuscript (with page/line numbers)

---

## Field Conventions

{FIELD_CONVENTIONS_SECTION}

<!-- 
The synthesiser fills this section with patterns extracted from exemplar papers.
Each subsection below must contain SPECIFIC, ACTIONABLE rules — not vague guidance.
Every rule should include the threshold it met (≥70% convention / 50-69% default).

### Introduction Architecture
- Paragraph-by-paragraph functional template with specific paragraph counts
- Standard length range (paragraphs and approximate word count)
- Hook type conventions for this field
- Contribution statement: which paragraph, how framed, how many contributions
- Whether/where to preview results (and how much quantitative detail)
- Roadmap conventions (present? phrasing pattern?)
- Literature engagement level in intro vs. saved for later section

### Theory / Conceptual Framework
- Standalone section vs. woven into intro (which is standard?)
- Standard section title(s) used in this field
- Organization principle (thematic / debate-structured / funnel)
- How to bridge from theory to empirics:
  - For IB/Strategy: hypothesis derivation pattern, hypothesis format, tightness of derivation
  - For Trade Econ: model → estimating equation derivation, or conceptual argument → specification
- Expected length relative to paper
- Number of subsections typical

### Empirical Strategy Presentation
- Standard sub-section order and titles
- Estimating equation: how formally presented, numbered, variable definitions location
- Identification defense: how many paragraphs, what threats to address, how prominently
- Data description: separate section? summary statistics table? sample construction detail?
- Variable construction: where defined (text / table / appendix)

### Results Narration
- Table reference conventions (exact phrasing patterns)
- Coefficient narration pattern:
  - Column-by-column vs. variable-by-variable vs. hypothesis-driven
  - How magnitudes are stated (β = value? direction only? percentage change?)
  - How significance is communicated (stars / p-values / "significant at X%")
  - How economic magnitudes are translated (one-SD? percentage of mean? dollar amount?)
  - How control variables are handled
- Robustness check placement and transition phrasing
- Heterogeneity analysis structure
- Mechanism/channel analysis structure and placement
- Economic magnitude discussion (where and against what benchmark)

### Discussion / Conclusion
- Separate discussion vs. combined vs. conclusion only (which is standard?)
- Standard paragraph structure with function assignments
- Policy implications: expected? how specific?
- Limitations: framing, length, placement
- Future research: expected? how connected to limitations?
- Final sentence/paragraph convention

### Citation Practices
- Expected reference count range
- Dominant in-text integration pattern (parenthetical clusters vs. narrative engagement)
- String citation conventions (how many refs per cluster?)
- Citation density distribution across sections
- Recency expectations

### Voice and Style
- Person: "we" / impersonal / mixed
- Active vs. passive balance
- Hedging level (confident / moderate / heavy)
- Signposting density
- Transition patterns between sections
- Tense conventions by section
- Sentence and paragraph length tendencies
-->

---

## Narrating Regression Output (fixest + modelsummary)

When the user provides regression tables from R (fixest/modelsummary), follow these rules:

### Reading the Output

1. Identify the dependent variable and key independent variables
2. Note the fixed effects structure (firm FE, year FE, industry-year FE, etc.)
3. Identify the clustering level for standard errors
4. Track how specifications build across columns (baseline → adding FEs → adding controls → subsample)
5. Check for interaction terms and their interpretation

### Narration Template

{RESULTS_NARRATION_TEMPLATE}

<!--
The synthesiser fills this with field-specific patterns extracted from exemplars.

Trade Econ typical pattern:
- Lead with the table: "Table 3 presents our main results."
- Introduce what the table tests in 1-2 sentences
- Walk column by column: "Column (1) shows the baseline OLS specification... Column (2) adds firm fixed effects..."
- State coefficient + significance: "The coefficient on tariff exposure is -0.124, statistically significant at the 1% level"
- Translate to economic magnitude: "A one-standard-deviation increase in tariff exposure is associated with a X% decline in export value relative to the sample mean"
- Controls noted briefly: "All specifications include [list of controls]"
- Key takeaway sentence at end of table discussion

IB/Strategy typical pattern:
- Lead with the hypothesis: "Hypothesis 1 predicted that tariff exposure would be negatively associated with..."
- Report support/non-support: "Consistent with Hypothesis 1, the coefficient on X is negative and significant (β = -0.15, p < 0.05), providing support for our prediction"
- May group results by hypothesis rather than by table column
- More interpretive narration connecting back to theoretical mechanism
- Control variables discussed if theoretically relevant
-->

### Handling Common fixest Patterns

- **Multiple fixed effects:** When `feols(y ~ x | firm + year)`, narrate: {FIELD_APPROPRIATE_FE_PHRASING}
- **Clustered SEs:** Note in methodology section and table notes; do not repeat in every results paragraph
- **Interaction terms:** Always narrate the economic meaning, not just statistical significance. Compute and report marginal effects at meaningful values.
- **Staggered DID (Sun-Abraham / Callaway-Sant'Anna):** Describe the methodological motivation before presenting results. Note the estimator used and why it's appropriate.
- **Event-study plots:** Describe pre-trends explicitly, then post-treatment dynamics. Reference specific time periods.

---

## Structural Templates

### Introduction Template

{INTRO_TEMPLATE}

<!--
Filled by synthesis. Example format:

| Para | Function | Length | Key Elements |
|------|----------|--------|--------------|
| 1 | Hook | 4-6 sentences | [field-specific hook type] |
| 2 | Literature & gap | 5-8 sentences | [how lit is positioned] |
| 3 | This paper's contribution | 3-5 sentences | [contribution framing pattern] |
| 4 | Preview of results | 3-4 sentences | [level of quantitative detail] |
| 5 | Data & identification preview | 2-3 sentences | [brief, not detailed] |
| 6 | Roadmap | 2-3 sentences | [standard phrasing] |
-->

### Conclusion Template

{CONCLUSION_TEMPLATE}

<!--
Filled by synthesis. Structure varies significantly between streams.
-->

---

## Anti-Patterns (What NOT to Do)

{ANTI_PATTERNS}

<!-- 
Filled by synthesis. Calibrated to specific field AND to common LLM failure modes:

### General LLM Anti-Patterns
- Do NOT write a literature review that reads like a list of paper summaries ("Author (Year) found X. Author (Year) found Y. Author (Year) found Z.")
- Do NOT use hedging language unusual for the field ("it could potentially be argued that...")
- Do NOT insert citations after every sentence — match the field's actual density
- Do NOT write an introduction longer than the field convention
- Do NOT use overly formal connectives ("Furthermore," "Moreover," "Additionally") in every paragraph transition
- Do NOT generate placeholder citations or fabricate author names
- Do NOT describe findings in prose that don't match the actual regression output

### Field-Specific Anti-Patterns

For Trade Econ:
- Do NOT include a standalone "Literature Review" section if the field doesn't use one
- Do NOT frame results around hypotheses if the paper has no formal hypotheses
- Do NOT over-interpret insignificant results ("while not statistically significant, the coefficient suggests...")
- Do NOT skip the economic magnitude translation
- Do NOT neglect the identification discussion

For IB/Strategy:
- Do NOT skip the theoretical contribution framing
- Do NOT present results without connecting back to hypotheses
- Do NOT write a minimalist conclusion — this field expects substantive discussion
- Do NOT present theory as a mere literature summary — it must build an argument toward hypotheses
- Do NOT ignore the "so what for managers/practitioners" in the discussion
-->

---

## Evidence Provenance Rules (for future literature processing mode)

When literature processing mode is implemented, it will enforce these rules on every claim extracted from a source paper. These rules are adapted from LitPilot's evidence provenance system.

| Rule | Description | Format |
|------|------------|--------|
| **Page binding** | Every extracted finding includes the page number | `(p. 14)` or `(pp. 8-9)` or `(page unclear)` |
| **Evidence tracing** | Each finding includes a verbatim quote or close paraphrase | `"inequality decreased by 12%"` or `[paraphrase] regional gaps narrowed` |
| **Source attribution** | Every claim tagged as author-stated or model-inferred | `[PAPER STATES]` or `[MODEL INFERS]` |
| **Uncertainty flagging** | Ambiguous or weakly supported claims flagged | `[UNCERTAINTY] — conflicting results in Tables 3 and 5` |

Each processed paper will also produce an Evidence Provenance Summary — a compact table listing every key claim with its page reference, source type, and uncertainty status.

---

## Collaboration Quality Assessment [HOOK — NOT IMPLEMENTED]

Reserved for future implementation. Borrowed from Academic Research Skills' 6-dimension collaboration quality assessment (direction-setting, intellectual contribution, quality control, iteration discipline, delegation efficiency, meta-learning). When implemented, this will run automatically after the writing pipeline completes.

---

## Generation Notes

This SKILL.md was generated by analyzing {N} exemplar papers from {JOURNAL_LIST}.
- Patterns marked as **rules** appeared in ≥70% of exemplars with medium-high convention confidence.
- Patterns marked as **defaults** appeared in 50-69% of exemplars.
- Patterns below 50% or rated as author-style were omitted.

Last generated: {DATE}

Exemplars analyzed:
{PAPER_LIST}

Synthesis report: See `synthesis_report.md` in the same directory for detailed pattern extraction decisions.
