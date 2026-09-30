---
WorkflowId: RepoStudy-Planning
Category: Study
Version: 1.0.0
---

# RepoStudy — Planning

## Prerequisites
- Targets resolvable: repo URL reachable via host fetch tools, or valid local path.
- Description states: topic name, rule prefix (2-6 uppercase letters + `-R`), extraction focus.
- No existing `ACC/Reference/Upstream.<Name>.Rules.md` for the same target (one home per rule — `SAFE-R10`).

## Plan
1. **Recon sweep** — README, LICENSE, `requirements.txt`/`setup.py`/`package.json`, entrypoints, `configs/`, `scripts/`, data folders. Record raw notes with file paths.
2. **Candidate list** — for each observed convention, write: candidate title + evidence path + failure mode it prevents.
3. **Scope filter** — drop anything that is operational misuse detail (`SAFE-R08`); keep engineering/eval/safety lessons.
4. **Numbering plan** — reserve `R01..R<NN>` block; confirm prefix unused.
5. **Artifact outline** — sections for Study.md + rules-file skeleton per `OUT-R03`.

## Risks
| Risk | Mitigation |
|---|---|
| Repo too large to read fully | Recon = structure + key files; evidence cites what was actually read |
| License ambiguous | STOP and report; do not proceed (`SAFE-R09`) |
| Rules duplicate existing ones | Cross-check `ACC/Reference/` first; reference by ID instead |
| Scope creep into misuse content | Scope gate in §0 of main workflow; default = exclude |

## Success Criteria
- [ ] Candidate list ≥ quality threshold for the stated focus (no padding).
- [ ] Numbering block reserved, prefix unique.
- [ ] Scope filter applied and documented.
