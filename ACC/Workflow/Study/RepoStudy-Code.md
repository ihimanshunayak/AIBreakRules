---
WorkflowId: RepoStudy-Code
Category: Study
Version: 1.0.0
---

# RepoStudy — Code (execution step)

> "Code" in this workflow = **authoring the two artifacts** (study doc + rules file). No executable code is required.

## Implementation

1. **Author `Knowledge/<Topic>/Study.md`**
   - Sections: What the repo is; Structure map (tree + purpose per area); Key engineering observations; Evaluation methodology; Reproducibility notes; Safety/ethics notes; References.
   - Every observation carries a path pointer. No unsourced claims (`OUT-R04`).

2. **Author `ACC/Reference/Upstream.<Name>.Rules.md`**
   - Header: source link, studied-at commit/sha, license + attribution line, scope note, ID-stability line.
   - Rules: `## <PREFIX>-R<NN> — <Title>` → Statement / Evidence / Why / Applied (`OUT-R03`).
   - Anti-patterns table: observed defect → this repo's counter-rule.
   - Citation block (BibTeX or formal reference).

3. **Register**
   - `ACC/Reference/Workflow.Index.md` → Studies Completed table (add row).
   - `README.md` → §3 Source Studies table (add row).

4. **Forbid** — no upstream code copied into this repo; no operational payloads/datasets; no secrets.

## Gates
- **Scope check:** grep draft for misuse markers (payload/prompt-injection instructions) → must be absent.
- **Integrity check:** every rule ID sequential; every Evidence pointer names a file that was actually inspected.
- **Cross-ref check:** all relative links resolve; index/README rows added.
