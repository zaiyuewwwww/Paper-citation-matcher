---
name: paper-citation-matcher
description: Use when the user wants sentence-level citation matching for an academic manuscript, especially for Word documents. Best for reading a specific manuscript section, preserving highlighted citations, prioritizing new literature, screening JCR Q1-Q2 literature, enforcing citation coverage sentence by sentence, limiting duplicate citations within a section and across the manuscript, generating a citation matching table, and optionally writing approved citations back into the manuscript with references.
---

# Paper Citation Matcher

## Overview

This skill supports sentence-level citation work for academic papers. It is designed for Word-based manuscripts where the user wants to build a literature matching table first, then optionally write citations back into a selected section while preserving highlighted sources, prioritizing new literature, controlling repetition, enforcing citation coverage, and recognizing user-marked sources inside manual brackets such as 【...】 and [...].

## When To Use

Use this skill when the user asks to:
- build a sentence-level or 1-2 sentence citation matching table
- supplement citations for a manuscript section
- preserve highlighted citations while improving references
- recognize manually marked sources inside `【】` or `[]` as already selected / already cited literature
- restrict sources to JCR Q1-Q2
- limit duplicate citations within the section
- check full-manuscript citation frequency before recommending literature
- enforce citation density, such as requiring at least one citation per sentence or per two-sentence block
- write matched citations back into a `.docx` manuscript
- verify citation quality, JCR quartiles, citation coverage, and text-reference correspondence

This skill is especially appropriate for requests like:
- "read this section and build a sentence-level literature matching table"
- "preserve my highlighted citations and improve the rest"
- "screen to JCR Q1-Q2 and avoid repeated citations"
- "write the approved citations back into the Word document"
- "check whether the citations are repetitive or mismatched"
- "make sure every sentence or every two sentences has at least one citation"

## Workflow

### Mode 1: Build Citation Matching Table

1. Read only the requested manuscript section.
2. Split the section into sentence-level or 1-2 sentence blocks.
3. Identify highlighted citations, highlighted source titles, and user-marked sources inside `【】` or `[]`, and preserve them whenever possible.
4. When `【】` or `[]` are present, treat them as a strong manual signal that the source has already been selected or cited.
5. Within `【】` or `[]`, recognize sources in this priority order: article title first, full bibliographic reference second, author-year citation third.
6. Map those manually marked sources into the manuscript's already-used citation pool whenever they can be resolved with high confidence.
7. Count existing citation frequency across the full manuscript before recommending citations.
8. Search for new, directly relevant papers first.
9. Reuse already cited papers only when they are clearly superior matches, foundational references, or necessary for continuity.
10. Recommend 1-2 real, directly relevant papers per block.
11. Avoid repeating the same citation within the section unless the user explicitly asks for repetition.
12. Prefer JCR Q1-Q2 literature when requested.
13. Run a citation coverage pass across the full section.
14. Flag any sentence that remains unsupported, or any two-sentence span without at least one clearly relevant citation.
15. Export a citation matching table.

### Mode 2: Write Citations Back Into the Manuscript

1. Use the approved citation table only.
2. Modify only the requested section.
3. Preserve original sentences as much as possible.
4. Make minimal wording changes only when needed for natural citation placement.
5. Preserve highlighted citations when the evidence is still appropriate.
6. Write citations into the requested section.
7. Run a second citation coverage pass after insertion.
8. At this coverage stage, ensure every sentence, or at minimum every two consecutive sentences, has at least one clearly relevant citation, without prioritizing repetition control yet.
9. If any sentence still lacks support, add a citation or citation cluster to that sentence even if that temporarily creates repetition.
10. After coverage is complete, run a repetition pass across the section and the manuscript-level citation pool.
11. Replace overused citations with new, suitable literature whenever possible, while preserving fit, evidence quality, and requested quartile rules.
12. Add missing references at the end of the manuscript.
13. Keep the rest of the manuscript unchanged.

### Mode 3: Final Citation Audit

1. Run a coverage audit first and check whether each sentence, or at minimum every two consecutive sentences, has at least one supporting citation.
2. If coverage gaps remain, add citations before evaluating repetition.
3. After coverage is complete, check whether the section contains duplicate citations or whether any source exceeds the manuscript-level repetition threshold.
4. Replace overused citations with new, suitable literature whenever possible, and keep repeated citations only when a clear reuse rationale exists.
5. Check whether each citation matches the sentence’s actual claim.
6. Check whether cited journals satisfy the requested quartile rule.
7. Check whether in-text citations and reference entries correspond one-to-one.
8. Update the citation matching table so it reflects coverage-pass additions, repetition-pass replacements, and any repeated citations that remain.
9. If problems are found, fix them directly in the target document.

## Core Rules

- Prefer direct evidence over vaguely related evidence.
- Prioritize citation novelty: when multiple suitable papers exist, prefer a new paper that matches the sentence claim well over an already cited paper.
- Reuse an already cited paper only if it is substantially more direct, conceptually foundational, methodologically canonical, or uniquely appropriate for the sentence.
- If an already cited paper is reused, explicitly record the reuse rationale in the citation matching table.
- Do not recommend fabricated, uncertain, or weakly matched citations.
- If evidence is not strong enough, say so and recommend rewriting the sentence instead of forcing a citation.
- Preserve user-highlighted citations when they are valid and meet the requested evidence standard.
- Treat content inside `【】` or `[]` as a strong manual signal that a source has already been selected or cited.
- Within `【】` or `[]`, prioritize recognition in this order: article title, full bibliographic reference, author-year citation.
- If a title inside `【】` or `[]` can be matched to a manuscript reference entry with high confidence, treat that title-based match as primary even when an author-year interpretation is also possible.
- If a full reference appears inside `【】` or `[]`, use it to resolve the source directly before attempting author-year parsing.
- If a manual marker contains a partial title or abbreviated title, attempt conservative title matching against the reference list before falling back to author-year parsing.
- Count manually marked sources inside `【】` or `[]` as part of the already-used citation pool for repetition control whenever they can be resolved with high confidence.
- If a manual marker cannot be resolved confidently, label it as a low-confidence or unresolved manual marker rather than silently ignoring it.
- Enforce citation coverage: each sentence should have at least one supporting citation, or at minimum every two consecutive sentences must share at least one clearly relevant citation.
- Short transition sentences may share citations with adjacent sentences only when they belong to the same argument and are clearly covered by the same evidence.
- Track repetition at two levels:
  - section level: avoid repeating the same citation within the target section
  - manuscript level: avoid pushing already overused citations when suitable alternatives exist
- After citations are inserted, always apply this order:
  - coverage pass first
  - repetition pass second
  - citation-table update pass third
- During the coverage pass, do not let repetition concerns prevent the section from reaching the minimum citation-density standard.
- During the repetition pass, replace citations that exceed the repetition threshold when a suitable new source can be found and matched appropriately.
- After the repetition pass, update the citation table to record any new citations added during coverage checking, any replacements made during repetition checking, and any repeated citations that remain with an explicit reuse rationale.
- When the user asks for a matching table only, do not modify the manuscript.
- When the user asks to write citations back into the manuscript, do not change unrelated sections.

## JCR Filtering Rule

For quartile screening, use a user-provided or project-provided JCR file whenever available.

Recommended configuration:
- worksheet: the sheet containing journal names and quartiles
- journal-name column: the column holding full journal titles
- quartile column: the column holding quartile values such as `Q1`, `Q2`, `Q3`, `Q4`

Default behavior:
- If the user requests Q1-Q2 screening and a JCR source is available, retain only journals matched to `Q1` or `Q2`.
- If a journal cannot be matched confidently, label it `unverified` instead of dropping it silently.
- Prefer exact journal-title matching first.
- If no JCR file is available, continue the citation workflow but mark quartile status as `not checked`.

For detailed quartile handling, read:
[references/jcr_rule.md](references/jcr_rule.md)

## Literature Search Integration

When the task requires systematic literature search or evidence screening, pair this skill with whatever literature-search workflow is available in the user's environment.

Recommended supporting tasks:
- search for candidate papers
- screen relevance conservatively
- verify paper details and links
- support Q1-Q2 filtering with a user-provided JCR source when available
- prefer new literature before reusing already cited sources

## Output Format

When building a citation table, use the schema in:
[references/output_schema.md](references/output_schema.md)

The default output should include:
- sentence block
- recommended literature
- links
- journal and quartile
- basis for citation
- evidence type
- whether highlighted source was retained
- whether the source was manually marked in `【】` or `[]`
- whether the manual marker was resolved confidently
- whether a new citation was preferred
- whether the paper was already cited in the manuscript
- manuscript citation count
- reuse rationale
- citation coverage status
- whether additional citation is needed
- coverage note
- whether the citation was added during the coverage pass
- whether the citation was replaced during the repetition pass
- whether a repeated citation was retained

## References

For the complete operating procedure, read:
- [references/workflow.md](references/workflow.md)
- [references/jcr_rule.md](references/jcr_rule.md)
- [references/output_schema.md](references/output_schema.md)
