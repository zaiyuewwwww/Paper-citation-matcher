# [README.md](https://github.com/user-attachments/files/26885465/README.md)
# Paper Citation Matcher

`paper-citation-matcher` is a Codex skill for sentence-level citation matching in academic manuscripts, especially `.docx` papers.

It is designed for workflows where you want to:

- build a citation matching table before editing the manuscript
- preserve manually selected or highlighted citations
- prioritize new literature over already used sources
- control duplicate citation use within a section and across a manuscript
- enforce citation coverage sentence by sentence
- optionally write approved citations back into the manuscript

This public version is intended for reuse and adaptation. Some behaviors reflect the original author's preferred writing workflow, but they are explicitly documented below so other users can adjust them.

## What The Skill Does

The skill supports three linked modes:

1. `Build Citation Matching Table`
   - read one section of a manuscript
   - split it into sentence-level or 1-2 sentence blocks
   - identify already used sources
   - recommend 1-2 suitable papers per block
   - export a structured citation table

2. `Write Citations Back Into the Manuscript`
   - use the approved citation table only
   - insert citations into the selected section
   - preserve the original wording as much as possible
   - add missing references

3. `Final Citation Audit`
   - check citation coverage
   - check repetition
   - check fit between sentence and citation
   - check quartile rules
   - check text-reference correspondence
   - update the citation table so it reflects all final changes

## Core Workflow

The intended execution order is:

1. Read the target section.
2. Detect already used citations, highlighted citations, and manual source markers.
3. Build a sentence-level citation matching table.
4. If approved, write citations back into the manuscript.
5. Run a coverage pass first.
6. Run a repetition pass second.
7. Update the citation table third.
8. Run a final audit.

This order is deliberate:

- `coverage pass first`
  ensures every sentence, or at minimum every two consecutive sentences, has at least one clearly relevant citation
- `repetition pass second`
  reduces overused citations only after the section is fully covered
- `citation-table update pass third`
  keeps the matching table aligned with the final manuscript

## Defaults That Reflect The Original Author's Habits

The following defaults are not universal publishing rules. They reflect the original author's preferred drafting workflow and can be changed if needed.

### 1. Manual source markers inside `【】` or `[]`

This skill treats content inside `【】` or `[]` as a strong signal that a source has already been selected or cited.

Examples:

- `【Insomnia, emotions, and job satisfaction】`
- `【Scott, B.A., Judge, T.A., 2006. Insomnia, emotions, and job satisfaction: A multilevel study.】`
- `【Scott and Judge, 2006】`
- `[Good gig, bad gig]`

Recognition priority is:

1. article title
2. full bibliographic reference
3. author-year citation

This means title markers are treated as the strongest manual signal.

Why this exists:

- many users write candidate sources in `【】` or `[]` during drafting
- those sources should count as already selected literature
- the skill should avoid re-recommending them as if they were new

How to customize:

- if you do not use `【】` or `[]` at all, you can remove or soften this rule in `SKILL.md`
- if you want author-year markers to be treated the same as titles, change the priority order
- if you want `【】` to mean "candidate only" rather than "already used", revise the wording in the manual-marker rules

### 2. New literature first

The skill prefers new literature over already cited literature when multiple papers are equally suitable.

Why this exists:

- to avoid over-relying on a few familiar sources
- to improve literature breadth
- to reduce repetitive citation patterns in one section or across a manuscript

How to customize:

- if you prefer canonical sources over novelty, weaken the `citation novelty` rule
- if you want stricter reuse control, define a lower manuscript-level repetition threshold

### 3. Coverage before repetition

After citations are inserted, the skill fixes citation gaps before cleaning repetition.

Why this exists:

- missing citations are a more immediate problem than repeated citations
- a section should first meet the minimum citation-density standard
- repetition can be cleaned once coverage is complete

How to customize:

- if you prefer stricter anti-repetition behavior, you can merge coverage and repetition checking
- if you want a looser standard, you can relax the coverage rule from sentence-level to paragraph-level

### 4. Citation density standard

Default standard:

- ideally every sentence has a citation
- minimum acceptable standard: every two consecutive sentences share at least one clearly relevant citation

Short transition sentences may share citations with adjacent sentences when they belong to the same argument.

How to customize:

- if you want a stricter standard, require one citation per sentence without exception
- if you want a looser standard, allow paragraph-level coverage instead

## JCR / Quartile Filtering

This public version does not hard-code a personal local JCR file path.

Instead, quartile filtering works like this:

- if the user provides a JCR or quartile table, the skill can use it
- if no quartile source is available, the workflow continues and quartile status is marked as `not checked`
- if matching fails, quartile status should be marked as `unverified`

Recommended configuration:

- one sheet with journal titles
- one column for journal names
- one column for quartiles such as `Q1`, `Q2`, `Q3`, `Q4`

## Citation Matching Table Output

The default table is designed to be audit-friendly. It records not only which paper is recommended, but why.

Default fields include:

- `Block`
- `Original sentence block`
- `Recommended literature`
- `Link(s)`
- `Journal / JCR quartile`
- `Basis for citation`
- `Evidence type`
- `Highlighted source retained?`
- `Manually marked in 【】 / []?`
- `Manual marker resolved?`
- `Already cited in manuscript?`
- `Citation count in manuscript`
- `New citation preferred?`
- `Reuse rationale`
- `Citation coverage status`
- `Needs additional citation?`
- `Coverage note`
- `Added in coverage pass?`
- `Replaced in repetition pass?`
- `Repeated citation retained?`

## Recommended Usage Pattern

The safest usage pattern is:

1. build the citation matching table first
2. review the recommendations
3. write citations back into the manuscript
4. run the final citation audit

This keeps the workflow transparent and makes it easier to review why each citation was chosen.

## Example Prompts

### Build a matching table only

```text
Please use paper-citation-matcher to read the Discussion section of my manuscript, detect manually marked sources inside 【】 and [], and build a sentence-level citation matching table. Prioritize new Q1-Q2 literature and avoid unnecessary repetition.
```

### Write citations into the manuscript

```text
Please use paper-citation-matcher to write the approved citations back into the Theory and Hypotheses section. Preserve my highlighted citations when still appropriate and add missing references at the end.
```

### Run the final audit

```text
Please use paper-citation-matcher to audit the updated section: fix citation gaps first, then reduce repeated citations, then update the citation matching table.
```

## Repository Structure

```text
paper-citation-matcher-public/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── workflow.md
    ├── jcr_rule.md
    └── output_schema.md
```

## Adapting This Skill For Your Own Workflow

The easiest places to customize are:

- `SKILL.md`
  for main workflow rules and defaults
- `references/workflow.md`
  for execution order and audit logic
- `references/jcr_rule.md`
  for quartile-screening rules
- `references/output_schema.md`
  for citation-table fields
- `agents/openai.yaml`
  for the skill's UI-facing display name, short description, and default prompt

If your drafting habits differ from the defaults in this repository, revise those rules explicitly rather than relying on hidden assumptions.
