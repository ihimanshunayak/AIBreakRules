# Workflows — CyberSecurityEngineer

> The 12 specialist workflows of the CyberSecurityEngineer agent. Each defines: objective → required context → stages → tools → outputs → validation → failure conditions. The live SSOT is the agent file §4/§5; this document expands each workflow for reference.

---

## Common Contract (applies to all workflows)

- **Boot first**: agent §0 sequence completed (governance loaded, repo inspected, task classified).
- **Scope gate**: environment class (§1 of agent file) established before any active step.
- **Evidence labels**: every claim Observed / Inferred / Suspected / Confirmed / Not validated.
- **Outputs exist on disk** before a workflow reports success (`OUT-R01`, `OUT-R07`).
- **Failure**: named `CYBER_AGENT_*` code + cause + unblocking input. No silent stops.

---

## 1) SecurityCodeReview

| Field | Definition |
|---|---|
| Objective | Evidence-backed security review of a repository/module with a prioritized remediation plan |
| Required context | Target path; stack facts (language, framework, runtime, test framework); scope class C0 (default) |
| Stages | 1. Repo recon 2. Architecture discovery 3. Attack-surface mapping 4. Input/output boundaries 5. AuthN 6. AuthZ 7. Data validation 8. Secrets 9. Dependencies 10. Infrastructure 11. Finding validation 12. Remediation |
| Tools | read, search (+ execute for SAST/test commands when available) |
| Outputs | Findings register (agent §5 anatomy) + remediation plan + patches for accepted fixes |
| Validation | Every finding has evidence pointer; confidence labels present; no speculative claim presented as confirmed |
| Failure conditions | Target unresolvable (`CYBER_AGENT_REPO_NOT_FOUND`); stack undeterminable (`CYBER_AGENT_CONTEXT_MISSING`) |

## 2) WebSecurityAssessment

| Field | Definition |
|---|---|
| Objective | Review/assess a web application's security surfaces |
| Required context | App path + run instructions OR target URL (C3/C4 only); scope confirmation |
| Stages | Surface map (routes, forms, uploads, cookies, headers) → auth/session analysis → input handling → CSRF/CORS posture → file handling → business logic → findings |
| Tools | read, search; execute (local run); web (advisory lookup) |
| Outputs | Surface map + findings register + hardening patch where in-repo |
| Validation | Findings verified against actual code/config; dynamic findings marked with environment used |
| Failure conditions | No runnable local path and target not authorized (`CYBER_AGENT_AUTH_SCOPE_MISSING`) |

## 3) APISecurityAssessment

| Field | Definition |
|---|---|
| Objective | Security review of REST/GraphQL APIs |
| Required context | API code or spec (OpenAPI/GraphQL schema); auth model |
| Stages | Endpoint inventory → authN per endpoint → object-level authZ matrix (who can access what) → input validation → rate limiting → error leakage → findings |
| Tools | read, search (+ execute for local test calls at C1) |
| Outputs | Endpoint × control matrix + findings register |
| Validation | Each endpoint row backed by a code/spec pointer; verified gaps listed |
| Failure conditions | Spec and code disagree (report both, don't pick silently) |

## 4) CloudSecurityReview

| Field | Definition |
|---|---|
| Objective | Review cloud configuration/IaC for security issues |
| Required context | IaC files (Terraform/Bicep/CloudFormation) or exported config; target provider |
| Stages | Inventory → IAM analysis (least privilege) → storage permissions → network exposure → secrets handling → container posture → findings + corrected IaC |
| Tools | read, search, web (provider doc verification) |
| Outputs | Config findings + corrected IaC snippets/patches |
| Validation | Each finding cites the config line + provider documentation basis |
| Failure conditions | Config unavailable in repo and not provided (`CYBER_AGENT_CONTEXT_MISSING`) |

## 5) DependencyAudit

| Field | Definition |
|---|---|
| Objective | Dependency inventory + known-vulnerability analysis + upgrade plan |
| Required context | Lockfiles/manifests (package-lock, uv.lock, composer.lock, go.sum, ...) |
| Stages | Inventory → direct/transitive split → advisory lookup (web/OSV/NVD) → reachability triage → upgrade path (breaking-change notes) → verification steps |
| Tools | read, search, execute (audit commands like `npm audit`, `pip-audit` when available), web |
| Outputs | Dependency register + prioritized upgrade plan + verification commands |
| Validation | Advisory IDs cited with source; severity methodology named; reachability statements labeled |
| Failure conditions | Network unavailable for advisories → mark `Not validated` with the stale-data caveat (`CYBER_AGENT_TOOL_FAILURE`) |

## 6) ThreatModeling

| Field | Definition |
|---|---|
| Objective | Structured threat model for a system/feature |
| Required context | System description + architecture artifacts in repo |
| Stages | Assets → actors → trust boundaries → data flows → STRIDE-style threats → likelihood/impact → mitigations → residual risks |
| Tools | read, search |
| Outputs | Threat model document (tables) with mitigations mapped to components |
| Validation | Every threat ties to an asset + entry point; mitigations name concrete controls |
| Failure conditions | System boundary unclear (`CYBER_AGENT_CONTEXT_MISSING` — one question) |

## 7) VulnerabilityValidation

| Field | Definition |
|---|---|
| Objective | Convert a suspected vulnerability into Confirmed or Refuted |
| Required context | The suspicion + the specific code/config to check; scope class for any execution |
| Stages | Hypothesis → minimal check design → check execution (static or C1/C2 sandbox) → result classification → evidence capture |
| Tools | read, search, execute (bounded, local/CTF only) |
| Outputs | Validation record: check performed, result, evidence, final label |
| Validation | The record alone lets a reviewer re-run the check |
| Failure conditions | Check requires unauthorized environment → `CYBER_AGENT_AUTH_SCOPE_MISSING`; result stays Suspected |

## 8) SecurePatch

| Field | Definition |
|---|---|
| Objective | Minimal secure fix for a confirmed/inferred vulnerability + regression test |
| Required context | Finding reference + target files + test framework |
| Stages | Confirm finding → design minimal fix → implement → regression test (fails pre-fix where feasible) → run tests → review diff → document |
| Tools | read, edit, search, execute |
| Outputs | Patch + test + rationale (mapped to the finding ID) |
| Validation | Test command run with quoted result; diff limited to the finding's scope |
| Failure conditions | Test suite unavailable → state `CYBER_AGENT_TEST_FAILURE` equivalent honestly ("not run: no suite; manual steps provided") |

## 9) SecurityAutomation

| Field | Definition |
|---|---|
| Objective | Build or repair security tooling: linters, scanners, CI gates, scripts |
| Required context | Target pipeline/CI + language stack + false-positive tolerance |
| Stages | Understand current pipeline → design gate (what fails the build) → implement → tune false positives → document → wire into CI |
| Tools | read, edit, search, execute |
| Outputs | Working scripts/pipeline config + docs + example output |
| Validation | Run the tool against the repo; false-positive rate assessed against known-clean code |
| Failure conditions | CI platform unavailable locally → provide config + local verification instructions, labeled honestly |

## 10) CTFLab

| Field | Definition |
|---|---|
| Objective | Analyze a local challenge repo / lab target; explain the intended vulnerability and the defensive lesson |
| Required context | Challenge path (C2) or owned lab (C3); platform context |
| Stages | Recon the challenge → identify category (web/crypto/rev/pwn/forensics/misc) → locate the intended flaw → explain the vulnerability class + why systems fall to it → show the fix/hardening lesson |
| Tools | read, search, execute (local challenge only) |
| Outputs | Walkthrough at defensive/educational level + hardened-variant notes |
| Validation | Explanation traced to the actual challenge artifacts |
| Failure conditions | Challenge expects remote target outside C2 scope (state the boundary) |

## 11) ReverseEngineeringAnalysis

| Field | Definition |
|---|---|
| Objective | Static analysis of a binary/format in a CTF/owned context |
| Required context | Artifact path + context (C2/C3) |
| Stages | File identification → strings/structure → control-flow intent → key routines → findings + reproducible steps |
| Tools | read, execute (local tooling when available) |
| Outputs | Annotated findings + reproduction steps |
| Validation | Steps repeatable by the user on the same artifact |
| Failure conditions | No local tooling for the format → static-only analysis, capability gap stated plainly |

## 12) SecurityReport

| Field | Definition |
|---|---|
| Objective | Consolidate a completed review/engagement into a professional report |
| Required context | Completed findings register(s) + validation records |
| Stages | Executive summary → scope/methodology → findings (agent §5 anatomy) → remediation plan (prioritized) → retest plan → appendix (tools, evidence index) |
| Tools | read, search, edit |
| Outputs | Report document at a registered path |
| Validation | Every finding carries evidence level; no claim exceeds its evidence; retest steps concrete |
| Failure conditions | Findings incomplete/never validated → report states that explicitly (never upgrades evidence levels in prose) |

---

## Workflow Selection Guide

| Request pattern | Workflow |
|---|---|
| "review this repo/module" | `SecurityCodeReview` |
| "review this web app / endpoint set" | `WebSecurityAssessment` |
| "review our API / GraphQL" | `APISecurityAssessment` |
| "audit our Azure/AWS/Terraform" | `CloudSecurityReview` |
| "check our dependencies" | `DependencyAudit` |
| "threat model this feature" | `ThreatModeling` |
| "is this really vulnerable?" | `VulnerabilityValidation` |
| "fix this vuln" | `SecurePatch` |
| "build a scanner/lint gate" | `SecurityAutomation` |
| "help me with this CTF room" | `CTFLab` |
| "analyze this binary" | `ReverseEngineeringAnalysis` |
| "write up the findings" | `SecurityReport` |
