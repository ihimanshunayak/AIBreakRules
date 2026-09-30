# ACC / Workflow — Index

> Cross-cutting workflows for maintaining AIBreakRules. One folder per workflow domain, 4-file ACC pattern per workflow.

## Domains

| Domain | Folder | Purpose |
|---|---|---|
| `Study/` | `Study/` | Study external repositories → Knowledge doc + numbered rules file |

## Workflows

| WorkflowId | Status | Files | Purpose |
|---|---|---|---|
| `RepoStudy` | **Active** | `Study/RepoStudy.md` + `-Planning` + `-Code` + `-Documentation` | Extract evidence-backed rules from an external repo (defensive scope only) |

## File Pattern

```
<Domain>/
├── <Name>.md               ← main workflow (frontmatter + gates + process)
├── <Name>-Planning.md      ← prerequisites, plan, risks, success criteria
├── <Name>-Code.md          ← execution steps + gates
└── <Name>-Documentation.md ← closing report + update checklist
```

## Rules

- Workflows are documented processes — every step auditable, no hidden state.
- New workflows: create all 4 files + register in `ACC/Reference/Workflow.Index.md` + here.
