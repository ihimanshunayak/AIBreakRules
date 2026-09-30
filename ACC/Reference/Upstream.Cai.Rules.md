# ACC / Reference / Upstream — CAI (Cybersecurity AI) Rules

> **Rules extracted from:** [`aliasrobotics/cai`](https://github.com/aliasrobotics/cai) — "Cybersecurity AI (CAI)", the open framework for AI Security (archived research artifact, v1.1.5 final snapshot).
> **Source studied at:** archival commit `6dc79257777f5f1c9500b4d2319935d34a47412e` (branch `archive`, 2026-08-22). **License:** MIT **+ proprietary additions** (research-only) — `LICENSE` / `LICENSE-MIT` / `DISCLAIMER`; attribution preserved (`SAFE-R09`). **Paper:** arXiv 2504.06017 (Mayoral-Vilches et al.).
> **Scope note:** CAI is agentic security tooling (offensive + defensive). This document extracts **agent-architecture, guardrail, evaluation, deployment, and lifecycle rules** for defensive/educational use (`SAFE-R08`). No exploit payloads, prompts, or operational content is reproduced; observations stay at the architecture and governance layer.
> Rule IDs (`CAIR-R*`) are stable — never reuse, never renumber.

---

## CAIR-R01 — Agentic systems are injection surfaces; guardrails are in scope

**Statement:** Any agent that consumes external content (tool output, web pages, scan results, documents) MUST treat prompt injection as a first-class threat and route untrusted content through dedicated guardrails before it reaches the model's decision loop.
**Evidence:** `README.md` §"What CSI fixes that CAI could not" — CAI's "four-layer guardrail framework" ([arXiv:2508.21669](https://arxiv.org/abs/2508.21669)) was a research contribution; `docs/guardrails.md` + `docs/cai_prompt_injection.md` documented it. The paper "Hacking the AI Hackers via Prompt Injection" empirically demonstrated that AI security tools are themselves injectable.
**Why:** The moment an agent reads untrusted text, an attacker can steer it; without guardrails the agent becomes the vulnerability it was built to find.
**Applied:** `CyberSecurityEngineer` treats all tool/scan output as untrusted input; findings about prompt injection in reviewed code are first-class findings; AIBreakRules keeps the guardrail lesson as an extracted rule, not as imported payloads.

---

## CAIR-R02 — Tool registry: capabilities as registered, introspectable components

**Statement:** Agent tools are registered in a central registry with stable names and discoverable metadata — never hard-wired call sites scattered through the loop.
**Evidence:** `src/cai/tool_registry.py` (6 KB) — central registration module for toolsets; `src/cai/tools/` holds tool implementations; `hooks/unleash.py`-documented tool categories in `docs/cai_architecture.md` (Command & Control tools, Recon tools, Exploitation tools, ...).
**Why:** A registry makes tools enumerable, testable, and removable; scattered call sites make the system unauditable and impossible to restrict.
**Applied:** This repo's agents treat their VS Code tool set as an explicit, minimal, declared capability surface (`tools:` frontmatter); `AIPA-R08` (tool definitions as data) is the upstream sibling rule.

---

## CAIR-R03 — Model-agnostic routing through one proxy (300+ providers via LiteLLM)

**Statement:** Model access is routed through a single abstraction layer supporting many providers and local models, so the system is never coupled to one vendor'S API shape.
**Evidence:** `README.md` §reference material — "**Models and providers** — 300+ via LiteLLM" (`docs/models.md`, `docs/providers/`); `src/cai/util/` + config carry `CAI_MODEL`; the archive documents configuring OpenAI, Anthropic, DeepSeek, Ollama via environment.
**Why:** Provider APIs drift and availability changes; a single routing layer keeps the agent functional across models and enables cost/telemetry control in one place.
**Applied:** `CyberSecurityEngineer` is model-agnostic by contract — it does not assume one model's context size, reasoning style, or tool behavior; `AIPA-R12` (multi-provider adapters) is the upstream sibling rule.

---

## CAIR-R04 — Human-in-the-loop control is an explicit, configurable mode

**Statement:** Autonomous execution and human-gated execution are both first-class modes; the gate is configuration, not code changes, and non-interactive runs are a declared mode (`HITL` toggle / headless parity).
**Evidence:** `README.md` §reference material — "**Architecture** — agents, tools, handoffs, patterns, tracing, **HITL**" (`docs/cai_architecture.md`); `src/cai/cli.py` (interactive REPL) vs `src/cai/cli_headless.py` (125 KB headless runner) — the same agent loop, two control modes.
**Why:** Security automation that cannot be paused before consequential steps is unsafe for real environments; a mode switch keeps one codebase auditable for both.
**Applied:** `CyberSecurityEngineer` requests/verifies authorization context before active-testing tasks; destructive or live-environment actions are always approval-gated (`SAFE-R07`).

---

## CAIR-R05 — Composable agents via explicit handoffs; single-responsibility teammates

**Statement:** Multi-agent systems compose specialists through declared handoff contracts; each agent has one clear responsibility, and orchestration is separate from the specialist logic.
**Evidence:** `docs/agents.md`, `docs/handoffs.md`, `docs/multi_agent.md` — dedicated documentation for the agent/handoff pattern; `src/cai/agents/` (derived from `openai/openai-agents-python`, MIT, per `LICENSE-MIT`); `agent_customization.py` for user-defined agents; `agents.yml.example` for declarative agent config.
**Why:** Specialists with explicit contracts can be tested, replaced, and reasoned about; untyped "do everything" agents fail unpredictably.
**Applied:** `AIPA-R07` + `PGPT-R02` (one agent, one contract; role separation) — the same principle at three different scales (runtime, session, and repository agents like RuleSmith vs CyberSecurityEngineer).

---

## CAIR-R06 — Framework lifecycle is documented: predecessor → framework → successor

**Statement:** A framework's role in a research/product lineage is written down (predecessor work, successor product, what the successor fixes), so adopters know exactly what they are depending on and why it may end.
**Evidence:** `README.md` §"The arc" — explicit timeline PentestGPT (USENIX Sec '24) → CAI (this repo) → G-CTR + CSI (successor); §"What CSI fixes that CAI could not" — a precise inventory (maintained injection defenses, supported release train, sovereignty, multi-scaffold, etc.); §"Precursor" — credits PentestGPT as the foundation.
**Why:** Undocumented lineage leads adopters to build on abandoned code; documented lineage converts an ending project into a research asset with a migration path.
**Applied:** `README.md` §3 + `Knowledge/_Index.md` of this repo record exactly which upstream was studied, when, and why; `PGPT-R16` (deprecation marking) is the sibling rule at file level.

---

## CAIR-R07 — Archived = frozen and labeled; unmaintained code carries a runtime warning

**Statement:** When a project stops being maintained, the repository states it in the README with a prominent warning (no fixes, isolated-environment use only, migration target), and the tree is preserved read-only rather than deleted.
**Evidence:** `README.md` top-level `[!IMPORTANT]` block — "📦 This repository is archived. No further releases, bug fixes, security patches or support will be provided." plus `[!WARNING]` "An archived offensive-security framework is **unmaintained attack tooling**... Run it only in isolated environments, against systems you are explicitly authorised to test."
**Why:** Users who pull an unmaintained tool into production inherit silent risk; a loud, in-tree label is the cheapest possible fix, and freezing beats deleting for reproducibility.
**Applied:** `README.md` §8 of this repo records the archived status + the professional successor (CSI) for the CAI study; `PGPT-R01` (research-prototype labeling) is the sibling rule.

---

## CAIR-R08 — Licensing that fits use: split MIT components from research-only additions

**Statement:** Repositories mixing community and restricted code state the partition explicitly (which paths are MIT, which are proprietary, what "research purposes only" means) and ship the disclaimer that governs use.
**Evidence:** `README.md` §"License and disclaimer" — "combines MIT-licensed components — derived from `openai/openai-agents-python`, under `src/cai/agents` — with proprietary additions licensed for research purposes only. See `LICENSE` · `LICENSE-MIT` · `DISCLAIMER`."
**Why:** Ambiguous licensing blocks legitimate reuse and invites misuse; a path-level partition lets downstream users reuse exactly what they are allowed to.
**Applied:** `SAFE-R09` — this repo preserves upstream notices per file and flags license gaps (e.g., `AIPA-R10`); the CAI study records the dual-license partition explicitly.

---

## CAIR-R09 — Security tooling is staged by environment maturity (dev container → isolated lab → real targets)

**Statement:** Development, testing, and operational execution of security tooling happen in progressively more real environments, each with explicit isolation guarantees — never straight into production.
**Evidence:** `.devcontainer/` in the repo root (dev sandbox); README usage warning "Run it only in isolated environments, against systems you are explicitly authorised to test, and never as part of a production security programme."; `docs/cai_installation.md` covers OS X / Ubuntu / Windows WSL / Android.
**Why:** Attack tooling pointed at real systems has side effects that cannot be undone; the staging discipline is what makes the tool legal and safe to develop.
**Applied:** `CyberSecurityEngineer` classifies every security task by environment (code-only, local lab, online CTF, owned infra, authorized engagement) before choosing active vs passive techniques.

---

## CAIR-R10 — Quality gates include secret scanning (gitleaks) and pinned dependencies (uv.lock)

**Statement:** Repositories that handle credentials run automated secret scanning in the pipeline (config committed in-repo) and pin the full dependency graph via lockfile.
**Evidence:** `.gitleaks.toml` (1.6 KB, committed config) at repo root; `uv.lock` (841 KB) at repo root; `Makefile` present for standard entrypoints.
**Why:** Security tooling inevitably touches credentials; one accidental commit of a key is unrecoverable without rotation. Lockfiles make builds reproducible and supply-chain diffs reviewable.
**Applied:** `SAFE-R05` (secrets discipline) + `.gitignore` secrets section in this repo; `JBB-R16` (lockfile reproducibility) is the sibling rule.

---

## CAIR-R11 — Research output documented as a first-class deliverable

**Statement:** Projects that claim capability publish the measurement apparatus: papers, benchmarks, datasets, and competition results are linked from the README with identifiers (arXiv IDs, DOIs), and the repository ships the benchmark harness.
**Evidence:** `README.md` §"The research" — 18 papers listed with arXiv links; `src/cai/caibench/` + `benchmarks/` in-tree; `CITATION.cff` for machine-readable citation; §"Competition record" with leaderboard links and paper references for every claim.
**Why:** Capability claims without public measurement are marketing; linking the harness + papers lets third parties reproduce or falsify results.
**Applied:** `JBB-R09`/`JBB-R18` + `PGPT-R18` (eval ships with tool) — this domain rule generalizes those to full research programs.

---

## CAIR-R12 — Configuration through environment variables with committed examples

**Statement:** Runtime configuration comes from environment variables (or equivalent local files), with a committed sanitized example file that documents every knob; the real file is git-ignored.
**Evidence:** `.env.example` (900 bytes, committed) at repo root; README usage snippet sets `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, `OLLAMA`, `CAI_STREAM`, etc. via `.env`; `docs/environment_variables.md` documents the full set; `CAI_MODEL` / `CAI_LICENSE_OFF` env switches.
**Why:** Config-as-environment keeps secrets out of the tree while making setup reproducible from the example alone.
**Applied:** `AIPA-R02` — sanitized example config committed, live config local-only; this is the third independent upstream confirming the pattern (now cross-referenced, not re-derived).

---

## CAIR-R13 — Cost, telemetry, and pricing are routed and observable

**Statement:** The model-routing layer owns cost accounting and telemetry in one place; pricing tables are maintained data, not scattered constants.
**Evidence:** `src/cai/pricings/` + root `pricings/` directories — structured pricing data; README (CSI layer table) — "a local proxy owning telemetry and cost"; `src/cai/output.py` (15 KB) for output handling.
**Why:** Agentic security runs burn tokens fast; without central cost ownership, spend is invisible and unbounded.
**Applied:** `JBB-R15` (API cost transparency) is the sibling rule; future automation in this repo that calls models must report its cost surface.

---

## CAIR-R14 — Structured error taxonomy (`errors.py`)

**Statement:** Errors are defined as named, stable codes in a dedicated module — not as ad-hoc strings — so failures are classifiable and testable.
**Evidence:** `src/cai/errors.py` (2.7 KB) — dedicated error definition module in the package root.
**Why:** Named error codes make automation (retry, escalation, reporting) possible and make failure-mode analysis a query rather than a grep.
**Applied:** `AIBR_ERR_*` codes + `CYBER_AGENT_*` codes in this repo's agents follow the same pattern at the instruction layer.

---

## CAIR-R15 — Subsystem-per-directory layout with stable public entrypoints

**Statement:** Large agent frameworks organize by subsystem (`agents/`, `tools/`, `repl/`, `api/`, `util/`, `tui/`), each with one clear job, and expose stable top-level entrypoints (`cli.py`, package `__init__.py`) rather than one giant module.
**Evidence:** `src/cai/` directory listing — `agents/ api/ caibench/ ctr/ continuous_ops/ internal/ pricings/ prompts/ repl/ sdk/ tools/ tui/ util/` + root modules `cli.py`, `config.py`, `config_loader.py`, `error.py`(s), `output.py`, `parallel_worker.py`, `tool_registry.py`; `src/cai/__init__.py` (stable surface).
**Why:** Subsystem boundaries contain blast radius and let contributors work in parallel; the observable anti-pattern is the opposite — one 125 KB `cli_headless.py` (present here) which the subsystem layout otherwise avoids.
**Applied:** `LLMA-R03`-style layout discipline in this repo's ACC/Knowledge split; note also the observed anti-pattern (mega-module) recorded in the study doc.

---

## CAIR-R16 — Predecessor credit and downstream warning: study the line, not just the repo

**Statement:** When a project borrows architecture from upstream open source, it credits the source in the README (not just the license file), and when it is superseded, the README points adopters at the successor.
**Evidence:** `README.md` §"Acknowledgements" — credits `openai/swarm` and `openai/openai-agents-python` for agentic principles, `LiteLLM` for routing, `phoenix` for tracing, and PentestGPT "where this line of research began"; §"Using the archive" points to CSI for anything beyond isolated research use.
**Why:** Credit keeps the ecosystem navigable; succession pointers prevent new adopters from starting on a dead end.
**Applied:** This repo's `Upstream.*.Rules.md` files + README §3 table credit every studied source with commit + license; `SAFE-R09`.

---

## Anti-Patterns Observed (do not repeat)

| Observation | Source | Our counter-rule |
|---|---|---|
| `cli_headless.py` grew to ~125 KB (single mega-module) | `src/cai/cli_headless.py` (size metadata) | `CAIR-R15` — subsystem-per-directory; split before modules exceed reviewable size |
| Dual `pricings/` trees (root + `src/cai/pricings/`) | repo listing | `SAFE-R10` — one home per artifact |
| Dual license without per-file headers for the partition | `LICENSE` + `LICENSE-MIT` + README prose only | `CAIR-R08` — state partition at path level in README (present) **and** consider per-file SPDX headers (recommendation) |
| Archived tooling still installable from PyPI with only README-level warnings | README `[!WARNING]` + PyPI `cai-framework` | `CAIR-R07` — warning is present at both; keep this pattern, and prefer successor-pointer in package metadata too |

## Attribution & Citation

- **Repository:** https://github.com/aliasrobotics/cai (archived; branch `archive`, commit `6dc79257777f5f1c9500b4d2319935d34a47412e`)
- **License:** MIT (components under `src/cai/agents`, derived from `openai/openai-agents-python`) + proprietary research-only additions — see upstream `LICENSE`, `LICENSE-MIT`, `DISCLAIMER`. Attribution preserved (`SAFE-R09`); no proprietary text reproduced.
- **Framework paper:**

```
@article{mayoral2025cai,
  title={CAI: An Open, Bug Bounty-Ready Cybersecurity AI},
  author={Mayoral-Vilches, V{\'\i}ctor and Navarrete-Lozano, Luis Javier and Sanz-G{\'o}mez, Mar{\'\i}a and Espejo, Lidia Salas and Crespo-{\'A}lvarez, Marti{\~n}o and Oca-Gonzalez, Francisco and Balassone, Francesco and Glera-Pic{\'o}n, Alfonso and Ayucar-Carbajo, Unai and Ruiz-Alcalde, Jon Ander and Rass, Stefan and Pinzger, Martin and Gil-Uriarte, Endika},
  journal={arXiv preprint arXiv:2504.06017},
  year={2025}
}
```

- **Guardrail research (referenced by CAIR-R01):** "Cybersecurity AI: Hacking the AI Hackers via Prompt Injection" — arXiv 2508.21669 (Mayoral-Vilches & Rynning, 2025).

## Related Rules

- `AIPA-R07` (one agent, one responsibility), `AIPA-R08` (tool definitions as data), `AIPA-R12` (multi-provider adapters), `AIPA-R02` (sanitized config), `AIPA-R10` (license gap rule)
- `PGPT-R01` (research prototype labeling), `PGPT-R02` (role separation), `PGPT-R16` (deprecation marking), `PGPT-R18` (eval ships with tool)
- `JBB-R15` (cost transparency), `JBB-R16` (lockfile reproducibility), `JBB-R18` (test tiers), `JBB-R09`/`JBB-R18` (self-evaluation)
- `SAFE-R05` (secrets), `SAFE-R07` (approval gates), `SAFE-R09` (attribution), `SAFE-R10` (one home per rule)
