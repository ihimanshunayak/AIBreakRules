---
WorkflowId: RepoStudy-Documentation
Category: Study
Version: 1.0.0
---

# RepoStudy — Documentation (closing step)

## Required Closing Report

1. **Artifacts written** — exact paths of: study doc, rules file.
2. **Rule summary** — count + one-line table of extracted rules (ID + title).
3. **Evidence integrity statement** — "every rule's Evidence pointer was verified against the studied source".
4. **Scope statement** — confirmation that extraction stayed defensive/educational (`SAFE-R08`).
5. **Registration confirmation** — index + README rows.
6. **Open questions / next studies** — anything ambiguous or deliberately deferred.

## Update Checklist
- [ ] `Workflow.Index.md` studies table updated
- [ ] `README.md` studies table updated
- [ ] Rule IDs note any reserved-but-unused block (if any)
- [ ] Commit message format: `Knowledge: add <Topic> study (defensive extraction)` or
      `Rules: add <PREFIX>-R* extracted from <repo>`

## Linkages
- Feeds: future `RuleAudit` runs (all rules must still carry evidence).
- Consumed by: any agent session whose task cites the new rule prefix.
