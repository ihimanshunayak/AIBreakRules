# ACC / Reference / Workflow.Index

> Registry of all workflows in AIBreakRules. One line per workflow; statuses: `Active` · `Draft` · `Planned`.

---

## 1) Registered Workflows

| WorkflowId | Status | File | Purpose |
|---|---|---|---|
| `RepoStudy` | **Active** | `ACC/Workflow/Study/RepoStudy.md` | Study an external repository and extract: (a) a Knowledge study doc, (b) a numbered `<PREFIX>-R*` rules file. Defensive/educational scope gate applies (`SAFE-R08`). |

**Invocation (for agents):**

```
Workflow(
  WorkflowId = "RepoStudy",
  Targets    = "<repo-url-or-local-path>",
  Description = "<topic name + extraction focus + desired rule prefix>"
)
```

Example:

```
Workflow(
  WorkflowId = "RepoStudy",
  Targets    = "https://github.com/llm-attacks/llm-attacks",
  Description = "Study for engineering rules. Topic: LlmAttacks. Rule prefix: LLMA-R. Output: Knowledge/LlmAttacks/Study.md + ACC/Reference/Upstream.LlmAttacks.Rules.md"
)
```

## 2) Planned Workflows

| Workflow | Purpose | Trigger |
|---|---|---|
| `RuleAdd` | Add a new rule to an existing domain file with full anatomy (`OUT-R03`) | When a new lesson/rules needs a home |
| `RuleAudit` | Periodic check: every rule has ID + evidence + Applied; no duplicates; no dead references | Monthly / after batch changes |
| `SkillExport` | Export a rule set into an agent-skill format (e.g., `.github/instructions/*.instructions.md`) | When rules must drive a specific tool |

## 3) Studies Completed

| # | Upstream | Rules file | Study | Date |
|---|---|---|---|---|
| 1 | [`llm-attacks/llm-attacks`](https://github.com/llm-attacks/llm-attacks) | `ACC/Reference/Upstream.LlmAttacks.Rules.md` (LLMA-R01..R20) | `Knowledge/LlmAttacks/Study.md` | 2026-09-30 |

## 4) Maintenance Rules

- New workflow ⇒ create the 4-file ACC set (`<Name>.md` + `-Planning` + `-Code` + `-Documentation`) and register here.
- Status changes are updated in this file in the same commit as the change.
- `Planned` entries describe intent only — no partial files on disk.
