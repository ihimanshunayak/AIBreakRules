# Workflow Template

> Copy 4 files to the target domain folder; fill each.
> Files: `<Name>.md` + `<Name>-Planning.md` + `<Name>-Code.md` + `<Name>-Documentation.md`.

---

## File 1: `<Name>.md`

```yaml
---
WorkflowId: <Name>
Type: Loop | Plural | Bootstrap | Audit | Update
Category: <Domain>            # full name, e.g. Study, Git, Governance
Status: Draft | Active | Deprecated
Version: 1.0.0
Intent: <one-line purpose>
Inputs:
  - <input description>
Outputs:
  Path: <target folder or file pattern>
  Artifacts:
    - <artifact description>
Calls:
  - <Name>-Planning
  - <Name>-Code
  - <Name>-Documentation
Requires:
  - ACC/Reference/Safety.md
  - ACC/Reference/Output.md
Refs:
  - <at least one real reference — SSOT doc or worked example>
Tags: [<tag1>, <tag2>]
---

# Workflow — <Name>

## 0) CRITICAL GATES
> Read Safety.md + Output.md to EOF before acting (SAFE-R02).
> - **NonNegotiables:** <this workflow's hard rules, up front>
> - **Stop conditions:** <hard stops + ambiguity stops>
> - **FV Final Verification:** <end-of-run verified checklist>

## 1) Purpose
<one paragraph>

## 2) Process
1. ...

## 3) Quality Gates
- ...

## 4) Anti-Patterns
- ...

## 5) Linkages
- Upstream: ...
- Downstream: ...
- References: ...
```

---

## File 2: `<Name>-Planning.md`

```yaml
---
WorkflowId: <Name>-Planning
Category: <Domain>
Version: 1.0.0
---

# <Name> — Planning

## Prerequisites
- ...

## Plan
1. ...

## Risks
| Risk | Mitigation |
|---|---|

## Success Criteria
- [ ] ...
```

---

## File 3: `<Name>-Code.md`

```yaml
---
WorkflowId: <Name>-Code
Category: <Domain>
Version: 1.0.0
---

# <Name> — Code (execution step)

## Implementation
<step-by-step with exact output paths>

## Gates
- <checks that must pass before completing>
```

---

## File 4: `<Name>-Documentation.md`

```yaml
---
WorkflowId: <Name>-Documentation
Category: <Domain>
Version: 1.0.0
---

# <Name> — Documentation (closing step)

## Required Closing Report
1. ...

## Update Checklist
- [ ] ...

## Linkages
- ...
```
