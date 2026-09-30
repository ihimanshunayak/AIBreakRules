# AIBreakRules — ACC Layer Instructions

> Loaded after the root `copilot-instructions.md`. Before any workflow execution.
> ACC = **AI Control Core** — the shared rules + workflows + templates layer of AIBreakRules.

---

## 1) ACC Layer Contents

```
ACC/
├── copilot-instructions.md     ← this file
├── Reference/                  ← THE RULES — SSOT (required reading)
│   ├── Safety.md               ← non-negotiable safety rules (SAFE-R*)
│   ├── Output.md               ← output quality standard (OUT-R*)
│   ├── Manifest.md             ← how to read manifest.json
│   ├── Workflow.Index.md       ← registry of all workflows
│   └── Upstream.LlmAttacks.Rules.md ← rules extracted from external repos (LLMA-R*)
├── Workflow/                   ← Repeatable processes (4-file pattern)
│   ├── _Index.md
│   └── Study/
│       ├── RepoStudy.md
│       ├── RepoStudy-Planning.md
│       ├── RepoStudy-Code.md
│       └── RepoStudy-Documentation.md
├── Template/                   ← Templates for new rules + workflows
│   ├── Rule.Template.md
│   └── Workflow.Template.md
└── Schema/                     ← Frontmatter schemas
    ├── Rule.Frontmatter.md
    └── Workflow.Frontmatter.md
```

## 2) What Belongs in ACC

- Rules that apply to **every** task in this repository.
- Workflows (repeatable, auditable processes) used for maintaining this repository.
- Templates + schemas that keep every new artifact consistent.

## 3) What Does NOT Belong in ACC

- Long-form study material → `Knowledge/` (loaded on demand, never always-on).
- One-off notes, scratch work, personal reminders.

## 4) Load Order (strict)

1. `Reference/Safety.md` — hard rules first.
2. `Reference/Output.md` — artifact quality bar.
3. `Reference/Manifest.md` — runtime config interpretation.
4. `Reference/Workflow.Index.md` — what processes exist.
5. `Reference/Upstream.LlmAttacks.Rules.md` — extracted upstream rules.

## 5) Rule ID Convention

Every rule has a stable ID `<DOMAIN>-R<NN>`. IDs are **never reused**; deprecated rules keep their ID and are marked `DEPRECATED`.

| Domain | File | Scope |
|---|---|---|
| `SAFE-R` | `Reference/Safety.md` | Safety, safety-gates, responsible AI |
| `OUT-R` | `Reference/Output.md` | Artifact quality and structure |
| `LLMA-R` | `Reference/Upstream.LlmAttacks.Rules.md` | Rules extracted from llm-attacks study |

## 6) Maintenance Rules

- **One rule, one home.** If a rule exists, do not duplicate it — reference it by ID.
- **Extraction ≠ copy.** Upstream content is summarized with evidence pointers, never pasted wholesale.
- **New rule domains** (new upstream study) get a new `Upstream.<Name>.Rules.md` file + an entry in `Workflow.Index.md` + README study table.
