---
WorkflowId: RepoStudy
Type: Plural
Category: Study
Status: Active
Version: 1.0.0
Intent: Study an external repository and extract a Knowledge study + a numbered rules file, under a defensive/educational scope gate.
Inputs:
  - repo URL or local path (Targets)
  - topic name + rule prefix + extraction focus (Description)
Outputs:
  Path: Knowledge/<Topic>/Study.md + ACC/Reference/Upstream.<Name>.Rules.md
  Artifacts:
    - Study.md (long-form study, on-demand knowledge layer)
    - Upstream.<Name>.Rules.md (numbered <PREFIX>-R* rules, SSOT)
    - Updated Workflow.Index.md studies table + README studies table
Calls:
  - RepoStudy-Planning
  - RepoStudy-Code
  - RepoStudy-Documentation
Requires:
  - ACC/Reference/Safety.md
  - ACC/Reference/Output.md
  - ACC/Workflow/Study/RepoStudy-Planning.md
  - ACC/Workflow/Study/RepoStudy-Code.md
  - ACC/Workflow/Study/RepoStudy-Documentation.md
Refs:
  - ACC/Reference/Upstream.LlmAttacks.Rules.md (worked example)
  - Knowledge/LlmAttacks/Study.md (worked example)
Tags: [Study, Rules, Knowledge, Extraction]
---

# Workflow — RepoStudy

## 0) CRITICAL GATES

> Read `ACC/Reference/Safety.md` + `ACC/Reference/Output.md` to EOF before acting (`SAFE-R02`).

- **NonNegotiables:**
  1. **Defensive/educational scope only** (`SAFE-R08`) — no operational attack tooling, payloads, or harmful data may be extracted or reproduced.
  2. **Summary over paste** (`OUT-R04`) — upstream text is paraphrased; quotes kept minimal.
  3. **Attribution preserved** (`SAFE-R09`, `LLMA-R02`) — license + paper citation captured in every rules file.
  4. **Every rule carries evidence** — `OUT-R03` anatomy: ID+Title / Statement / Evidence / Why / Applied.
- **Stop conditions:**
  - Target repo unreachable or license unclear ⇒ STOP, report.
  - Requested extraction drifts toward misuse content ⇒ STOP (`SAFE-R08`), ask for re-scope.
  - Rule prefix already exists in `ACC/Reference/` ⇒ STOP (one home per rule).
- **FV Final Verification:** rules file exists with sequential unbroken IDs; study file exists; both registered; no secrets; citations present.

## 1) Purpose

Turn "study this repo" into **durable, numbered, evidence-backed rules** — so knowledge doesn't evaporate after the session. Two artifacts, two layers: `Knowledge/` = learning; `ACC/Reference/` = enforceable rules.

## 2) Process

1. **Recon** — file tree (via host API or local walk), README, LICENSE, config/build files, entrypoints, docs.
2. **Extract candidates** — every convention → a candidate rule with evidence pointer (file path + what it shows).
3. **Annotate defensively** — for adversarial/security sources: keep rules engineering-level; exclude operational detail.
4. **Number & draft rules** — `<PREFIX>-R01..R<NN>` following `OUT-R03`; include a "Why" (failure mode prevented) and "Applied" (how this repo adopts it).
5. **Draft study doc** — narrative + structure map + links; long-form lives here, rules stay crisp.
6. **Register** — `Workflow.Index.md` studies table + README studies table.
7. **Verify** — FV checklist in §0.

## 3) Quality Gates

- [ ] Every rule has 5 anatomy fields, non-empty.
- [ ] Evidence pointers name real files (path + section).
- [ ] IDs sequential, no gaps, no renumbering of existing rules.
- [ ] Scope gate respected (defensive/educational).
- [ ] Cross-references resolve (indexes, README tables, links).

## 4) Anti-Patterns

- Pasting large upstream excerpts instead of summarizing (`OUT-R04`).
- Rules without evidence ("best practice" with no source pointer) — forbidden.
- Renaming existing rule IDs after publication (`SAFE-R10`).
- Silent scope-creep: extracting misuse techniques "just in case".

## 5) Linkages

- Upstream: user request naming a repo.
- Downstream: `Workflow.Index.md` (registration), README table, future `RuleAdd`/`RuleAudit` workflows.
- References: `Upstream.LlmAttacks.Rules.md` + `Knowledge/LlmAttacks/Study.md` (worked example of this workflow's output).
