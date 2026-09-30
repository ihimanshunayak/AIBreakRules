# AIBreakRules

> **AI Working Rules & Governance — SSOT repository.**
> **Owner:** Himanshu Nayak · **GitHub:** [@ihimanshunayak](https://github.com/ihimanshunayak)
> **Version:** 1.0.0 · **Last updated:** 2026-09-30

---

## 1) Purpose

AIBreakRules is a **single source of truth (SSOT)** for the rules of working with and around AI systems:

- **Engineering discipline** for AI/LLM projects — versioning, configuration, evaluation, reproducibility.
- **Safety & responsible-use rules** for AI research and tooling.
- **Governance structure** that AI agents (GitHub Copilot, Cyborg agents, etc.) load at session start and follow deterministically.

The repository mirrors the **Cyborg platform** structural pattern:

```
root bootstrap → manifest → ACC (shared rules layer) → workflows → knowledge
```

## 2) Why This Repo Exists

1. **Rules must be written.** Ad-hoc AI usage is not auditable. Rules live in files, get versioned, and get reviewed.
2. **Borrow from the best.** Well-engineered AI research codebases teach durable conventions. Every studied upstream repo gets a **rules extraction** under `ACC/Reference/`.
3. **One home per rule.** Every rule has exactly one location; agents read from here — not from memory.

## 3) Source Studies (upstream repos analyzed)

| Upstream | What it is | Rules extracted | Full study |
|---|---|---|---|
| [`llm-attacks/llm-attacks`](https://github.com/llm-attacks/llm-attacks) (CMU) | Adversarial-attack research on aligned LLMs (GCG) | [`ACC/Reference/Upstream.LlmAttacks.Rules.md`](ACC/Reference/Upstream.LlmAttacks.Rules.md) | [`Knowledge/LlmAttacks/Study.md`](Knowledge/LlmAttacks/Study.md) |
| [`prajwalsamsonck/AI-Pentest-Agent`](https://github.com/prajwalsamsonck/AI-Pentest-Agent) | LLM-assisted orchestration framework for authorized web-security assessments | [`ACC/Reference/Upstream.AiPentestAgent.Rules.md`](ACC/Reference/Upstream.AiPentestAgent.Rules.md) | [`Knowledge/AiPentestAgent/Study.md`](Knowledge/AiPentestAgent/Study.md) |
| [`BasiPT/PentestGPT`](https://github.com/BasiPT/PentestGPT) | LLM pentest-guidance research prototype (USENIX Security 2024) | [`ACC/Reference/Upstream.PentestGpt.Rules.md`](ACC/Reference/Upstream.PentestGpt.Rules.md) | [`Knowledge/PentestGpt/Study.md`](Knowledge/PentestGpt/Study.md) |
| [`JailbreakBench/jailbreakbench`](https://github.com/JailbreakBench/jailbreakbench) | Open robustness benchmark for LLM jailbreaks (NeurIPS 2024 D&B) | [`ACC/Reference/Upstream.JailbreakBench.Rules.md`](ACC/Reference/Upstream.JailbreakBench.Rules.md) | [`Knowledge/JailbreakBench/Study.md`](Knowledge/JailbreakBench/Study.md) |

> **Responsible-use notice.** Upstream *security/adversarial* research is studied here for **defensive and
> educational purposes only** — to derive engineering, evaluation, and safety **rules**. This repository must
> never contain operational attack tooling, harmful datasets, or exploit payloads. See `ACC/Reference/Safety.md` §6.

## 4) Repository Structure

```
AIBreakRules/
├── copilot-instructions.md       ← Root bootstrap — agents boot from here
├── manifest.json                 ← SSOT: identity, language, paths, approvals
├── README.md                     ← This file
├── LICENSE                       ← MIT
├── .gitignore
├── .github/
│   ├── copilot-instructions.md   ← Copilot integration pointer → root bootstrap
│   └── agents/
│       └── RuleSmith.agent.md    ← Resident agent: repo study + rule extraction + audit
│
├── ACC/                          ← Shared rules layer (AI Control Core)
│   ├── copilot-instructions.md   ← ACC bootstrap delta
│   ├── Reference/                ← RULE DOCS — the SSOT for every rule
│   │   ├── Safety.md             ← Non-negotiable safety rules
│   │   ├── Output.md             ← Output quality standard
│   │   ├── Manifest.md           ← How to read manifest.json
│   │   ├── Workflow.Index.md     ← Workflow registry
│   │   ├── Upstream.LlmAttacks.Rules.md ← Rules extracted from llm-attacks (LLMA-R01..R20)
│   │   ├── Upstream.AiPentestAgent.Rules.md ← Rules extracted from AI-Pentest-Agent (AIPA-R01..R16)
│   │   ├── Upstream.PentestGpt.Rules.md ← Rules extracted from PentestGPT (PGPT-R01..R18)
│   │   └── Upstream.JailbreakBench.Rules.md ← Rules extracted from JailbreakBench (JBB-R01..R19)
│   ├── Workflow/                 ← Repeatable workflows (4-file pattern)
│   │   ├── _Index.md
│   │   └── Study/RepoStudy.md    ← Study a repo & extract rules
│   ├── Template/                 ← Rule + Workflow templates
│   └── Schema/                   ← Frontmatter / file schemas
│
└── Knowledge/                    ← Long-form study material (on-demand)
    ├── _Index.md
    ├── LlmAttacks/Study.md       ← Full study of the llm-attacks repo
    ├── AiPentestAgent/Study.md   ← Full study of the AI-Pentest-Agent repo
    ├── PentestGpt/Study.md       ← Full study of the PentestGPT repo
    └── JailbreakBench/Study.md   ← Full study of the JailbreakBench repo
```

## 5) How To Use

**As a human:** start at `ACC/Reference/` — each file is a self-contained rule domain.

**As an AI agent (boot order):**

1. Read `copilot-instructions.md` (root bootstrap).
2. Read `manifest.json` (identity, language, approvals).
3. Load `ACC/copilot-instructions.md` + everything in `ACC/Reference/`.
4. Load `Knowledge/` only when the task references it.

**Adding a new rule:**

1. Pick the right home (existing domain file, or a new file in `ACC/Reference/`).
2. Follow `ACC/Template/Rule.Template.md`.
3. Register workflow changes in `ACC/Reference/Workflow.Index.md`.
4. Commit with a clear message — e.g., `Rules: add LLMA-R02 exact version pinning`.

**Adding a new upstream study:**

1. Run the `RepoStudy` workflow (`ACC/Workflow/Study/RepoStudy.md`).
2. Outputs: `Knowledge/<Topic>/Study.md` + `ACC/Reference/Upstream.<Name>.Rules.md`.
3. Register the study in this README's table + `ACC/Reference/Workflow.Index.md`.

## 6) Agents

| Agent | File | Purpose |
|---|---|---|
| **RuleSmith** | `.github/agents/RuleSmith.agent.md` | Resident AIBreakRules guardian — studies repositories, extracts evidence-backed rules (full anatomy), audits rule domains, writes Knowledge study docs, and enforces the defensive scope gate. |

**RuleSmith feature set (F1–F10):**

| # | Feature |
|---|---|
| F1 | Repo Study — structured recon → candidate rules (runs `RepoStudy` end-to-end) |
| F2 | Evidence-backed extraction — every rule carries ID/Statement/Evidence/Why/Applied |
| F3 | Rule authoring — add to domains or create `Upstream.<Name>.Rules.md`; stable IDs |
| F4 | Rule audit — ID sequence, evidence integrity, one-home-per-rule, attribution, scope |
| F5 | Knowledge study docs — structure maps, methodology, reproducibility, citations |
| F6 | Scope gate — defensive/educational only (`SAFE-R08`) |
| F7 | Registration & indexing — README/workflow/index updates on every change |
| F8 | Attribution & license discipline — notices, BibTeX, summary-over-paste |
| F9 | Git discipline — format, one-change-per-commit, no force-push |
| F10 | Language & output discipline — Hinglish default, complete artifacts only |

**Usage:** in VS Code chat, select **RuleSmith** from the agent picker, then e.g.

- `Study https://github.com/<org>/<repo> — topic <Topic>, prefix <PREFIX>-R`
- `Add rule to Safety.md: <lesson>`
- `Audit LLMA-R* — check IDs, evidence, duplicates`

## 7) Rule Numbering

Rules are numbered `<DOMAIN>-R<NN>`:

| Domain | Meaning | File |
|---|---|---|
| `SAFE-R` | Safety rules | `ACC/Reference/Safety.md` |
| `OUT-R` | Output rules | `ACC/Reference/Output.md` |
| `LLMA-R` | Rules extracted from llm-attacks | `ACC/Reference/Upstream.LlmAttacks.Rules.md` |
| `AIPA-R` | Rules extracted from AI-Pentest-Agent | `ACC/Reference/Upstream.AiPentestAgent.Rules.md` |
| `PGPT-R` | Rules extracted from PentestGPT | `ACC/Reference/Upstream.PentestGpt.Rules.md` |
| `JBB-R` | Rules extracted from JailbreakBench | `ACC/Reference/Upstream.JailbreakBench.Rules.md` |

## 8) License

MIT — see [`LICENSE`](LICENSE). Upstream study content retains upstream attribution
(llm-attacks © 2023 Andy Zou, MIT; PentestGPT © the PentestGPT authors, MIT; JailbreakBench © 2024 JailbreakBench, MIT; AI-Pentest-Agent has no repository-level license — see its rules file attribution note).
