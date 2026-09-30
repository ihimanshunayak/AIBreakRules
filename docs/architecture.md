# Architecture — CyberSecurityEngineer

> System architecture of the `CyberSecurityEngineer` VS Code custom agent: layers, boot flow, governance integration, and design decisions. Companion docs: `agent-behavior.md`, `workflows.md`, `security-model.md`, `upstream-research.md`, `testing.md`.

---

## 1) What This Agent Is

`CyberSecurityEngineer` is a **VS Code custom agent profile** (`.github/agents/CyberSecurityEngineer.agent.md`) — an instruction layer that shapes how GitHub Copilot's available model performs cybersecurity engineering work inside the user's repositories. It is not a standalone tool, service, or runtime; it owns no code execution surface of its own beyond the standard Copilot tool aliases (`read, edit, search, execute, web, todo`).

## 2) Layered Design

```
┌──────────────────────────────────────────────────────────────┐
│  VS Code / GitHub Copilot host                               │
│  (model selection, tool runtime, chat surface)               │
├──────────────────────────────────────────────────────────────┤
│  CyberSecurityEngineer.agent.md                              │
│  ├─ Boot sequence (§0)          — resolve repo + governance  │
│  ├─ Scope classification (§1)   — C0..C5 environment gating  │
│  ├─ Task classification (§2)    — 19 task classes            │
│  ├─ Evidence model (§3)         — Observed/Inferred/...      │
│  ├─ Workflows (§4/§5)           — 12 specialist workflows    │
│  ├─ Coding behavior (§6)        — repo-first engineering     │
│  ├─ Context mgmt (§7)           — targeted reads             │
│  ├─ Execution loop (§8)         — 8-stage loop               │
│  ├─ Model contract (§9)         — model-agnostic             │
│  ├─ Error codes (§10)           — CYBER_AGENT_*              │
│  ├─ Secrets + Git safety (§11)  — hard limits                │
│  ├─ Output style (§12)          — no fake completion         │
│  ├─ Inheritance (§13)           — AIBreakRules by reference  │
│  └─ Hard constraints (§14)      — NEVER list                 │
├──────────────────────────────────────────────────────────────┤
│  AIBreakRules governance layer (when present in workspace)   │
│  ├─ SAFE-R*  — safety, approvals, scope gate                 │
│  ├─ OUT-R*   — artifact quality, commits, verification       │
│  ├─ AIPA-R*, PGPT-R*, JBB-R*, CAIR-R* — engineering domains  │
│  └─ Knowledge/** — on-demand studies                         │
├──────────────────────────────────────────────────────────────┤
│  Target repository (the work actually being done)            │
│  code · config · tests · CI · infra                          │
└──────────────────────────────────────────────────────────────┘
```

## 3) Boot Flow

```
Session start
  │
  ├─ 1. Resolve workspace root + task target
  ├─ 2. Resolve AIBreakRules: workspace root? nested folder? absent?
  │        present → 3. Load copilot-instructions.md → manifest.json →
  │                    ACC/copilot-instructions.md → ACC/Reference/{Safety,
  │                    Output, Manifest, Workflow.Index}.md   (read to EOF)
  │        absent  → continue; note absence in final report
  ├─ 4. Inspect target repo: tree → targeted files → stack facts
  │        (no guessing; one focused question if facts are unavailable)
  ├─ 5. Apply language (manifest Developer.Language; default Hinglish)
  └─ 6. Classify task → select workflow → enter execution loop
```

Any failure in steps 1–4 stops the session with a `CYBER_AGENT_*` code — the boot sequence never proceeds on assumptions (`SAFE-R02`, `SAFE-R03` analogues).

## 4) Design Decisions (and why)

| Decision | Rationale | Origin |
|---|---|---|
| **Governance by reference, not duplication** | AIBreakRules rules get updated upstream; copies drift and violate one-home-per-rule (`SAFE-R10`) | AIBreakRules architecture |
| **Scope classes C0–C5 as the first gate** | Environment/authorization is the only honest way to separate legitimate security work from misuse; keyword-based filtering both over-blocks (security engineers type "exploit" all day) and under-blocks | Master requirements §5, §11; `PGPT-R01` context discipline |
| **Evidence labels on every security claim** | "Exploitable" without evidence is the classic failure mode this agent must never repeat | `JBB-R07` (validate your judge) generalized to claims; `OUT-R03` |
| **Model-agnostic contract** | Copilot models change; workflows must survive model swaps | `CAIR-R03` (routing-agnostic), `AIPA-R12` |
| **Tool output treated as untrusted** | AI security tooling is itself injectable (arXiv 2508.21669) | `CAIR-R01` |
| **Minimal declared tool set** | Smaller capability surface = more auditability | VS Code agents reference (minimal tools principle); `AIPA-R08` |
| **HITL as explicit mode, not an afterthought** | Live/destructive actions must be gated by configuration, not by luck | `CAIR-R04`; `SAFE-R07` |
| **No provider-safeguard entanglement** | Capability comes from decomposition/context/validation, never from defeating model policies | Master requirements §5; agent §9 |
| **Error codes over silent failure** | Classifiable failures are testable; silent failures corrupt trust in the whole deliverable | `CAIR-R14`, `AIBR_ERR_*` pattern |

## 5) Relationship to RuleSmith

Both agents live in `.github/agents/` and share the AIBreakRules boot contract, but they are **different specialists** (master plan §3):

```
AIBreakRules
     │
     ├── RuleSmith                → rules / repository studies / governance
     │      (rule extraction, audits, Knowledge docs, scope gate)
     │
     └── CyberSecurityEngineer    → cybersecurity engineering / analysis / authorized testing
            (secure code, reviews, findings, patches, threat models, CTF/lab)
```

- RuleSmith **writes the rules**; CyberSecurityEngineer **applies them to security engineering work**.
- Neither is a copy of the other; overlap is limited to the shared boot sequence and the defensive scope gate (both enforce `SAFE-R08`).
- The two agents may reference each other (e.g., CyberSecurityEngineer flags a missing rule for RuleSmith to add) but neither silently edits the other's artifact classes.

## 6) Extensibility

- **New workflows**: add to the agent's §4 table + `docs/workflows.md`; if the workflow becomes heavy, register it in AIBreakRules `ACC/Workflow/` via RuleSmith.
- **New rule domains**: extracted via RuleSmith's RepoStudy workflow; CyberSecurityEngineer references the new `*-R*` prefix in §13.
- **Tool changes**: when VS Code/Copilot changes the tool-alias set, update only the frontmatter `tools:` list and §9 — the rest of the architecture is tool-independent.
- **Future upstream studies**: follow `upstream-research.md` §4 template; each study produces a `Knowledge/<Topic>/Study.md` + `Upstream.<Name>.Rules.md` pair.

## 7) Non-Goals

- Not a runtime server, daemon, or browser automation harness.
- Not a substitute for professional legal authorization in C4 engagements — it verifies that authorization context *exists*, it does not grant it.
- Not a monetization or deployment vehicle; it is an instruction-layer artifact of the AIBreakRules repository.
