# ACC / Schema / Workflow Frontmatter

> YAML frontmatter schema for workflow files (`ACC/Workflow/<Domain>/<Name>.md`).

---

## 1) Required Fields (main `<Name>.md`)

| Field | Type | Values / Format |
|---|---|---|
| `WorkflowId` | string | Matches filename (without `.md`) |
| `Type` | enum | `Loop` \| `Plural` \| `Bootstrap` \| `Audit` \| `Update` |
| `Category` | string | Full-name domain (e.g., `Study`, `Git`) — never short codes |
| `Status` | enum | `Draft` \| `Active` \| `Deprecated` |
| `Version` | semver | `<major>.<minor>.<patch>` |
| `Intent` | string | One-line purpose |

## 2) Recommended Fields

| Field | Type | Format |
|---|---|---|
| `Inputs` | array | `[<input description>]` |
| `Outputs` | object | `{ Path: <pattern>, Artifacts: [...] }` |
| `Calls` | array | Sub-workflow filenames (without `.md`) |
| `Requires` | array | Repo-relative paths that must be read first |
| `Refs` | array | At least one real reference (SSOT doc or worked example) |
| `Tags` | array | `[<tag>]` |

## 3) Sub-file Fields (`-Planning`, `-Code`, `-Documentation`)

| Field | Required |
|---|---|
| `WorkflowId` | ✅ (e.g., `RepoStudy-Planning`) |
| `Category` | ✅ |
| `Version` | ✅ |

Sub-files do NOT need `Type`, `Status`, `Intent` — inherited from main.

## 4) Validation Checklist

- [ ] Frontmatter parses as YAML
- [ ] `WorkflowId` matches filename
- [ ] `Requires`/`Refs` paths resolve on disk
- [ ] Registered in `ACC/Reference/Workflow.Index.md` + `ACC/Workflow/_Index.md`
- [ ] `Version` bumped on any content change
