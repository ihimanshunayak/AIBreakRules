# Upstream Research — CyberSecurityEngineer

> Research record backing the CyberSecurityEngineer design: the repositories studied, the patterns adopted, and the evidence paths. Long-form studies live in `Knowledge/**`; this file is the index + adoption ledger (master-plan §24).

---

## 1) Study Ledger

| # | Upstream | Studied at | License | Study doc | Extracted rules |
|---|---|---|---|---|---|
| 1 | [`llm-attacks/llm-attacks`](https://github.com/llm-attacks/llm-attacks) | `11bc378`-era (v1.0.0 study) | MIT | `Knowledge/LlmAttacks/Study.md` | `LLMA-R01..R20` |
| 2 | [`prajwalsamsonck/AI-Pentest-Agent`](https://github.com/prajwalsamsonck/AI-Pentest-Agent) | `daf5e7639e2ddafce588c230b292be1434eff94f` | ⚠️ none (repo-level) | `Knowledge/AiPentestAgent/Study.md` | `AIPA-R01..R16` |
| 3 | [`BasiPT/PentestGPT`](https://github.com/BasiPT/PentestGPT) | `0f8e73a68efefe2e4d5e78b7ad3d7d7eb4332092` | MIT | `Knowledge/PentestGpt/Study.md` | `PGPT-R01..R18` |
| 4 | [`JailbreakBench/jailbreakbench`](https://github.com/JailbreakBench/jailbreakbench) | `23dbdf6b19650521604456229bc1d9c4156c85c1` | MIT | `Knowledge/JailbreakBench/Study.md` | `JBB-R01..R19` |
| 5 | [`aliasrobotics/cai`](https://github.com/aliasrobotics/cai) | `6dc79257777f5f1c9500b4d2319935d34a47412e` (archive) | MIT + research-only additions | `Knowledge/Cai/Study.md` | `CAIR-R01..R16` |

> Every study is defensive/educational only (`SAFE-R08`): engineering architecture, evaluation methodology, and governance — no payloads, prompts, or harmful data reproduced. Attribution preserved per `SAFE-R09`.

## 2) Adoption Ledger (rule → where CyberSecurityEngineer uses it)

| Rule | Adopted in the agent as |
|---|---|
| `CAIR-R01` (guardrails / injection defense) | §13 prompt-injection hygiene — tool/web/scan output is data, not instructions |
| `CAIR-R02` (tool registry) | §9: explicit, minimal declared tool surface |
| `CAIR-R03` (model-agnostic routing) | §9 model-agnostic contract; workflows survive model swaps |
| `CAIR-R04` (HITL as a mode) | §1/§11: approval gates as configuration; live actions stop for confirmation |
| `CAIR-R05` (handoffs / single responsibility) | Agent as specialist alongside RuleSmith (see `architecture.md` §5) |
| `CAIR-R06` (lineage documented) | `README.md` §3 + this ledger; successor/archival status recorded |
| `CAIR-R07` (archived = labeled) | CAI study records archived status + CSI successor |
| `CAIR-R08` (split licensing) | CAIR rules file records MIT/proprietary partition; attribution discipline |
| `CAIR-R09` (environment staging) | §1 scope classes C0→C5 with isolation doubling as staging discipline |
| `CAIR-R10` (gitleaks + lockfile) | §11 secret exposure handling; `DependencyAudit` workflow consumes lockfiles |
| `CAIR-R11` (research as deliverable) | This research + study docs pattern |
| `CAIR-R12` (env config + example) | §6 secure-by-default: env-only secrets; `.env.example` pattern in reviews |
| `CAIR-R13` (cost/telemetry ownership) | `DependencyAudit` + model-usage note in §9 (cost surface reported when automation calls models) |
| `CAIR-R14` (named error taxonomy) | §10 `CYBER_AGENT_*` error codes |
| `CAIR-R15` (subsystem layout) | §6 repo-first structure honoring target repo layout |
| `CAIR-R16` (credit + successor) | Attribution requirements in findings reports and studies |
| `AIPA-R01` (authorized-use framing) | §1 scope statement; C4/C5 gating |
| `AIPA-R03`/`AIPA-R04` (bounded loops, repeat detection) | §7 context discipline; automation workflows cap iterations |
| `AIPA-R05`/`AIPA-R06` (parsable output, delimited plans) | Finding-register and report formats (strict anatomy) |
| `AIPA-R07` (one agent, one contract) | Specialist roles: RuleSmith vs CyberSecurityEngineer |
| `AIPA-R08` (tool definitions as data) | §9 tool surface declared in frontmatter |
| `AIPA-R09`/`AIPA-R10` (attribution, license-gap rule) | Attribution in findings/reports; license checks in reviews of vendored code |
| `AIPA-R12`/`AIPA-R13` (multi-provider, fallback) | Model-agnostic contract; graceful downgrade when a capability is missing |
| `AIPA-R15` (dual-channel observability) | Completion reports mirror on-disk artifacts (claim ↔ file) |
| `PGPT-R01` (prototype labeling) | Honest capability claims throughout (§12) |
| `PGPT-R02` (role separation) | Review pipeline stages are separated (recon → surface → boundaries → findings → validation) |
| `PGPT-R03`/`PGPT-R04` (durable plan state, evidence-gated growth) | §7 working set in todo; findings grow only from observed facts |
| `PGPT-R05`/`PGPT-R07` (context pruning, lossless parsing) | Targeted reads; findings preserved verbatim in the register |
| `PGPT-R08`/`PGPT-R09` (versioned prompts, split model roles) | Workflow-version language in `workflows.md`; model-agnostic splitting of heavy/light steps |
| `PGPT-R10`/`PGPT-R11` (connection self-test, empirical model choice) | Boot verification of workspace/governance before work; capability honesty on failures |
| `PGPT-R12`/`PGPT-R13` (evaluable targets, mixed matchers) | Test-based validation in `SecurePatch`; check-style variety in `VulnerabilityValidation` |
| `PGPT-R15`/`PGPT-R16` (session logging, deprecation marking) | Reports written to disk; superseded guidance marked |
| `PGPT-R17` (containerized targets) | C1 local sandbox discipline |
| `PGPT-R18` (eval ships with tool) | `testing.md` scenario suite ships with the agent |
| `JBB-R04` (benign control set) | False-positive discipline in `SecurityAutomation` |
| `JBB-R05`/`JBB-R06` (typed judges, deterministic judges) | Deterministic validation commands; named severity methodology |
| `JBB-R07` (multi-judge + human majority) | Multi-source confirmation before Confirmed label |
| `JBB-R08` (degenerate pre-filters) | Trivial/duplicate finding suppression in reviews |
| `JBB-R09`/`JBB-R13` (self-evaluation, two-layer submission) | Findings self-validated before reports; register first, report second |
| `JBB-R11`/`JBB-R12` (consent, registered extension) | Workflow registration discipline (new workflows registered before use) |
| `JBB-R14`/`JBB-R15` (same-harness baselines, cost transparency) | Reproducible validation environments described in reports; cost surface stated for model-calling automation |
| `JBB-R16`/`JBB-R17`/`JBB-R18` (lockfiles, CI gates, test tiers) | `DependencyAudit` lockfile discipline; `SecurityAutomation` CI gates; test-tier honesty |
| `JBB-R19` (citation hygiene) | Full citation chains in reports/studies |
| `LLMA-R01`/`LLMA-R02` (cite paper, license compliance) | Attribution rules throughout |
| `LLMA-R03` (version pinning) | Dependency + tooling version pinning in automation |
| `LLMA-R08` (structured state) | Durable findings register across long reviews |
| `LLMA-R12` (bounded retries) | Automation iteration caps |
| `LLMA-R13` (criteria as artifacts) | Acceptance criteria → test artifacts in `SecurePatch` |
| `LLMA-R14` (versioned datasets) | Evidence index versioned in reports |
| `LLMA-R15`/`LLMA-R19` (stable public API) | Agent frontmatter contract stability (name/tools changes documented) |
| `LLMA-R20` (demo ≠ production) | Prototype/maturity labels on tooling deliverables |
| `SAFE-R05`/`SAFE-R06`/`SAFE-R07` | §11 secrets, git safety, approval gates |
| `SAFE-R08` | Global scope gate (defensive/educational) |
| `SAFE-R09`/`SAFE-R10` | Attribution; reference-not-duplicate inheritance (§13) |

## 3) Architecture-Only Sources (studied, not rule-extracted)

| Source | Why referenced | Where used |
|---|---|---|
| `openai/openai-agents-python` + `openai/swarm` | Agent/handoff architecture credited by CAI; handoff composition pattern | `architecture.md` §5 (agent relationships) |
| `BerriAI/litellm` | Multi-provider routing pattern credited by CAI | `CAIR-R03` evidence chain |
| `Arize-ai/phoenix` | Tracing/observability pattern credited by CAI | Observability guidance in reports |
| VS Code custom-agents documentation | Frontmatter schema: `description`, `name`, `tools`, `model`, `argument-hint`, `user-invocable`, hooks | Agent file frontmatter |

## 4) Template — Future Studies (RepoStudy contract)

When a new upstream is studied for this agent (via RuleSmith's RepoStudy workflow):

```
Repository:        <name> (<url>)
Studied at:        commit <sha> (<branch>, <date>)
License:           <license> — attribution preserved (SAFE-R09)
Architecture:      <entrypoints, agent loop, tools, config, model integration>
Patterns adopted:  <list → where adopted in the agent>
Anti-patterns:     <list → counter-rules>
Evidence paths:    <file paths inspected, recorded in the rules file>
Scope statement:   defensive/educational extraction only (SAFE-R08)
Artifacts:         Knowledge/<Topic>/Study.md + ACC/Reference/Upstream.<Name>.Rules.md
Registration:      README §3 + Workflow.Index §3 + Knowledge/_Index.md
```

## 5) Evidence Paths (what was actually inspected)

- CAI: `README.md` (archive notice, warnings, arcs, license partition, acknowledgements), root listing (`.env.example`, `.gitleaks.toml`, `uv.lock`, `LICENSE*`, `DISCLAIMER`, `agents.yml.example`, `CITATION.cff`), `src/cai/` listing (subsystems incl. `tool_registry.py`, `errors.py`, `cli.py`, `cli_headless.py`, `caibench/`, `pricings/`), archival commit metadata (`6dc7925...`).
- PentestGPT / AI-Pentest-Agent / JailbreakBench: as recorded in their respective rules files' Evidence fields.
- VS Code agent format: local Copilot extension docs (`agent-customization` skill + `references/agents.md`).
