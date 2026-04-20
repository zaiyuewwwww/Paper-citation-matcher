# Workflow

## Mode 1: Build Citation Matching Table Only

1. Read the specified section of the manuscript.
2. Extract sentence blocks at sentence-level or 1-2 sentence level.
3. Identify highlighted literature and preserve it when valid.
4. Scan `【】` and `[]` for manually marked sources.
5. Treat manually marked sources as already-selected literature, with recognition priority in this order: article title, full bibliographic reference, author-year citation.
6. Resolve those manual markers against the reference list whenever possible, and add them to the already-used citation pool.
7. Check full-manuscript citation frequency.
8. Search for new Q1-Q2 candidate papers first.
9. Reuse already cited papers only when they are clearly more direct, foundational, or uniquely appropriate.
10. Recommend 1-2 papers per block.
11. Avoid duplicate use within the section.
12. Run a citation coverage pass across the section.
13. Flag any sentence with no citation support, or any two-sentence span without at least one clearly relevant citation.
14. Export an xlsx matching table.

## Mode 2: Write Citations Back Into the Manuscript

1. Use the approved citation table only.
2. Modify only the requested section.
3. Preserve original sentences as much as possible.
4. Make minimal wording changes only when needed for natural citation placement.
5. Write citations into the section.
6. Run a second citation coverage pass after insertion.
7. Ensure every sentence, or at minimum every two consecutive sentences, has at least one clearly relevant citation.
8. If coverage gaps remain, add citations first and do not prioritize repetition control at this stage.
9. After coverage is complete, run a repetition pass across the section and the manuscript-level citation pool.
10. Replace overused citations with new, suitable literature whenever possible while preserving fit and requested quartile standards.
11. Add missing references at the end.
12. Keep the rest of the manuscript unchanged.

## Mode 3: Final Citation Audit

1. Run a coverage audit first and repair any remaining citation gaps.
2. After coverage is complete, check whether any citation is repeated within the section.
3. Check whether any citation exceeds the manuscript-level repetition threshold.
4. Replace overused citations with new, suitable literature whenever possible, and keep repeated citations only when a clear reuse rationale exists.
5. Check whether each citation matches the sentence claim directly.
6. Check quartile compliance using the local JCR file.
7. Check whether in-text citations and reference entries fully correspond.
8. Update the citation table so it records coverage-pass additions, repetition-pass replacements, and retained repeated citations.
9. If needed, fix the document directly and report what was changed.
