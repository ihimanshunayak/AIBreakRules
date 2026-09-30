# ACC / Reference / Manifest

> How to read and apply `manifest.json` — the SSOT for runtime behavior of this repository.

---

## 1) Purpose

`manifest.json` at the repository root is the **single source of truth** for:

- Who owns the repository and in which language agents respond.
- Which paths hold rules, workflows, templates, schemas, knowledge.
- Which actions require explicit human approval.
- Global quality + responsible-AI switches.

## 2) Field Reference

| Field | Meaning | Change Policy |
|---|---|---|
| `SchemaVersion` | Manifest format version | Bump on structural change; keep backward-compatible |
| `Repo` / `RepoVersion` | Repository name + version | Version bump on rule-set releases |
| `LastUpdated` | Date of last change | Update on every manifest edit |
| `Developer.Name` / `.Username` / `.Email` | Owner identity | Owner-only change |
| `Developer.Language` | Agent response language (`Hinglish` default) | Owner-only change |
| `Paths.*` | Canonical locations for each layer | Change = rule change; update references |
| `Approvals.ApprovalMode` | `manual` = risky actions wait for explicit approval | Owner-only change |
| `Approvals.RequireApprovalFor.*` | Per-action approval gates | Owner-only change |
| `Quality.*` | Quality switches (`NoSkeleton`, `FailFastOnMissingSSOT`, ...) | Should stay `true`; deviations need written justification |
| `ResponsibleAI.*` | Defensive-only scope gates (`DefensiveOnly`, `NoOperationalAttackTooling`, ...) | Should stay `true`; deviations need written justification |

## 3) Interpretation Rules

1. Read the manifest at session start (root bootstrap step 1).
2. `Approvals.RequireApprovalFor.<X> == true` ⇒ the action in `<X>` **must** stop for explicit approval (`SAFE-R07`).
3. If the manifest is missing or unparseable ⇒ fail-fast `AIBR_ERR_SSOT_MISSING`.
4. Manifest values never override `Safety.md` rules — Safety is supreme; the manifest configures, it does not weaken.

## 4) Change Protocol

1. Edit `manifest.json`.
2. Update `LastUpdated`.
3. If a path changed, update every reference (`copilot-instructions.md`, `ACC/copilot-instructions.md`, README).
4. Commit with message `Manifest: <what changed and why>`.
