# JCR Rule

Default source:
- Use a user-provided or project-provided JCR / quartile table when available.

Recommended fields:
- journal name column: full journal title
- quartile column: quartile label such as `Q1`, `Q2`, `Q3`, `Q4`

Rules:
- Prefer Q1 and Q2 only when the user requests upper-quartile filtering.
- If no exact match is found, mark the journal as `unverified`.
- Use exact journal-title matching first.
- Only relax quartile filtering when the user explicitly asks for broader inclusion.
- If no quartile file is available, continue the workflow and mark quartile status as `not checked`.
