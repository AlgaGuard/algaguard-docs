# Repository working rules

These rules apply to the complete `algaguard-docs` repository.

1. Phase 1 is documentation and planning only. Do not add application, firmware, infrastructure, or CI implementation files.
2. Preserve useful content, but current confirmed decisions override older drafts.
3. Use `CONFIRMED` for user-confirmed facts, `VENDOR-LISTED` for retailer claims, `TBD` for unresolved decisions, `ASSUMPTION` for engineering assumptions, and `SAFETY` for safety-critical statements.
4. Planned capabilities must be described as planned, not implemented.
5. Do not invent scientific algae thresholds or an unreviewed battery wiring design.
6. Use Mermaid for editable diagrams, simple professional English, ISO dates, and relative links between repository documents.
7. Maintain one authoritative document per topic and link to it from summaries.
8. Do not introduce Firebase, NestJS, Java/Spring Boot, or Python into the MVP architecture.
9. Do not make a git commit unless the user explicitly requests one.
10. Validate relative links and Mermaid blocks after documentation changes.
