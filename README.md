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
│   └── copilot-instructions.md   ← Copilot integration pointer → root bootstrap
│
├── ACC/                          ← Shared rules layer (AI Control Core)
│   ├── copilot-instructions.md   ← ACC bootstrap delta
│   ├── Reference/                ← RULE DOCS — the SSOT for every rule
│   │   ├── Safety.md             ← Non-negotiable safety rules
│   │   ├── Output.md             ← Output quality standard
│   │   ├── Manifest.md           ← How to read manifest.json
│   │   ├── Workflow.Index.md     ← Workflow registry
│   │   └── Upstream.LlmAttacks.Rules.md ← Rules extracted from llm-attacks
│   ├── Workflow/                 ← Repeatable workflows (4-file pattern)
│   │   ├── _Index.md
│   │   └── Study/RepoStudy.md    ← Study a repo & extract rules
│   ├── Template/                 ← Rule + Workflow templates
│   └── Schema/                   ← Frontmatter / file schemas
│
└── Knowledge/                    ← Long-form study material (on-demand)
    ├── _Index.md
    └── LlmAttacks/Study.md       ← Full study of the llm-attacks repo
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

## 6) Rule Numbering

Rules are numbered `<DOMAIN>-R<NN>`:

| Domain | Meaning | File |
|---|---|---|
| `SAFE-R` | Safety rules | `ACC/Reference/Safety.md` |
| `OUT-R` | Output rules | `ACC/Reference/Output.md` |
| `LLMA-R` | Rules extracted from llm-attacks | `ACC/Reference/Upstream.LlmAttacks.Rules.md` |

## 7) License

MIT — see [`LICENSE`](LICENSE). Upstream study content retains upstream attribution
(llm-attacks © 2023 Andy Zou, MIT license).
