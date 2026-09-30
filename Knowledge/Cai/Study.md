# Study — CAI / Cybersecurity AI (`aliasrobotics/cai`)

> **Source:** https://github.com/aliasrobotics/cai · studied at archival commit `6dc79257777f5f1c9500b4d2319935d34a47412e` (branch `archive`, 2026-08-22, v1.1.5 final snapshot)
> **License:** MIT components (under `src/cai/agents`, derived from `openai/openai-agents-python`) + **proprietary research-only additions** — see upstream `LICENSE`, `LICENSE-MIT`, `DISCLAIMER`. Attribution preserved.
> **Status:** **Archived** — no further releases, fixes, or support. Successor: Cybersecurity Superintelligence (CSI), Alias Robotics.
> **Scope:** defensive/educational extraction of agent-architecture, guardrail, evaluation, deployment, and lifecycle rules (`SAFE-R08`). No exploit payloads or operational content reproduced.
> **Extracted rules:** `ACC/Reference/Upstream.Cai.Rules.md` (CAIR-R01..R16)

---

## 1) What The Repository Is

An open agentic security framework (March 2025 → August 2026) for building and deploying **AI-powered offensive and defensive security automation** — agents, tools, handoffs, guardrails, human-in-the-loop control, model routing across 300+ providers via LiteLLM — released under Alias Robotics and co-funded by the EU EIC Accelerator (project RIS, GA 101161136). It established "Cybersecurity AI" as a research domain: 18 papers, 30+ CVEs, #1 rankings at Neurogrid / Dragos OT / HTB "AI vs Humans" CTFs. It is the direct successor of PentestGPT's research line and the predecessor of the commercial CSI platform.

**Why it is worth studying here:** it is the most completely-documented open example of an **engineering organization for agentic security**: tool registry, guardrail layers against prompt injection, HITL vs headless parity, benchmark harness in-tree, competition results linked to papers, and an honest archived-project lifecycle. Those are exactly the patterns the CyberSecurityEngineer agent and this repo's governance need. The CAIR rules are those patterns; the guardrail research (arXiv 2508.21669) is directly load-bearing for how our agent treats untrusted tool output (`CAIR-R01`).

## 2) Structure Map

```
cai/                                   (branch: archive, single archival commit)
├── README.md                          ← archive notice, CSI successor, research index (CAIR-R06/R07/R11/R16)
├── LICENSE / LICENSE-MIT / DISCLAIMER ← MIT + proprietary partition (CAIR-R08)
├── CITATION.cff                       ← machine-readable citation (CAIR-R11)
├── .env.example                       ← sanitized config template (CAIR-R12)
├── .gitleaks.toml                     ← secret-scanning config (CAIR-R10)
├── .devcontainer/                     ← dev sandbox (CAIR-R09)
├── agents.yml.example                 ← declarative agent config (CAIR-R05)
├── Makefile · pyproject.toml · uv.lock ← standard entrypoints + pinned graph (CAIR-R10)
│
├── src/cai/                           ← the package (CAIR-R15 subsystem layout)
│   ├── __init__.py                    ← stable public surface
│   ├── cli.py                         ← interactive REPL entry (CAIR-R04)
│   ├── cli_headless.py                ← headless entry (⚠️ ~125 KB mega-module — anti-pattern)
│   ├── config.py / config_loader.py / errors.py
│   ├── tool_registry.py               ← central tool registration (CAIR-R02)
│   ├── output.py / parallel_worker.py / continuation.py / agent_customization.py
│   ├── agents/                        ← agent abstractions (MIT, from openai-agents-python) (CAIR-R05)
│   ├── tools/                         ← tool implementations (CAIR-R02)
│   ├── prompts/ · repl/ · tui/ · api/ · sdk/ · util/ · internal/
│   ├── caibench/                      ← benchmark harness in-tree (CAIR-R11)
│   └── pricings/                      ← structured cost data (CAIR-R13)
│
├── benchmarks/ · docs/ (incl. guardrails.md, cai_prompt_injection.md,
│   agents.md, handoffs.md, multi_agent.md, environment_variables.md,
│   models.md, mcp.md, bencharking docs)         ← documented subsystems (CAIR-R04/R05/R12)
├── examples/ · fluency/ · media/ · pricings/ · tests/ · tools/
└── release_to_pypi_public.sh
```

## 3) Key Engineering Observations

1. **Guardrails as a designed layer** — four-layer framework against prompt injection validated in arXiv 2508.21669; the framework explicitly documents that AI security tools are injectable. → `CAIR-R01`
2. **Tool registry centralization** — `tool_registry.py` + `tools/` + documented tool categories; capability surface is enumerable. → `CAIR-R02`
3. **Model-agnostic via LiteLLM** — 300+ providers; local models supported; config through env vars. → `CAIR-R03`, `CAIR-R12`
4. **HITL and headless parity** — same loop, two control modes; human-gating is configuration. → `CAIR-R04`
5. **Agents + handoffs + multi-agent docs** — composition pattern documented first-class (`docs/agents.md`, `docs/handoffs.md`). → `CAIR-R05`
6. **Lineage honesty** — README states predecessor (PentestGPT), successor (CSI), and the exact inventory of what the successor fixes. → `CAIR-R06`
7. **Archived = frozen + loudly labeled** — `[!IMPORTANT]` archive block + `[!WARNING]` unmaintained-tooling warning; tree preserved read-only; PyPI stays for reproducibility. → `CAIR-R07`
8. **Dual-license partition documented** — MIT components credited to `openai-agents-python` at path level; research-only additions governed by DISCLAIMER. → `CAIR-R08`
9. **Secret scanning in pipeline** — committed `.gitleaks.toml`; pinned `uv.lock`. → `CAIR-R10`
10. **Research as deliverable** — 18 papers linked with arXiv IDs; benchmark harness ships in-tree; competitions linked to papers. → `CAIR-R11`
11. **Named error module** — `errors.py` defines stable error classes. → `CAIR-R14`
12. **Cost/telemetry ownership** — pricing data structured in-repo; successor documents proxy owning telemetry + cost. → `CAIR-R13`

## 4) Agentic-Security Architecture (as documented)

- **Agent composition**: specialist agents (Red Team, Defender, APT, Forensics, etc. named in the successor materials) composed via handoffs; `agents.yml.example` shows declarative customization.
- **Tool categories**: documented in architecture docs (C2, recon, exploitation, etc.) — registered centrally so the loop stays tool-agnostic.
- **Control modes**: interactive REPL (human-in-the-loop) vs headless runner — same agent loop.
- **Guardrail layer**: intercepts untrusted content before the decision loop; the framework's own research paper details the four-layer design and its empirical validation.
- **Model routing**: one config-driven layer (`CAI_MODEL` + provider keys) — the agent never hard-couples to a provider.

## 5) Evaluation & Research Methodology (as observed)

- **In-tree benchmark harness** (`src/cai/caibench/`, `benchmarks/`) + external meta-benchmark paper (CAIBench, arXiv 2510.24317).
- **Competition-measured claims**: every README performance claim (rank, flags, challenges, velocity) links to a paper or leaderboard.
- **Attribution/defense research pairing**: the injection paper both demonstrates the vulnerability class and validates defenses — offensive finding + defensive method shipped together.
- **Honest successor ledger**: "What CSI fixes that CAI could not" is effectively a published post-mortem of the framework's own limitations — a model for how to end a project.

## 6) Reproducibility & Deployment Notes (as documented)

- Python 3.12 recommended; `pip install cai-framework` (public line frozen at 0.5.10, December 2025); professional line v1.1.5 in this tree was never on public PyPI.
- Environment-driven configuration documented exhaustively (`docs/environment_variables.md`); `.env.example` committed; `CAI_LICENSE_OFF=1` documented for the public package path.
- Platform coverage documented: OS X, Ubuntu 20.04/24.04, Windows WSL, Android (`docs/cai_installation.md`).
- MCP integration documented (`docs/mcp.md`) — external tool integration path.

## 7) Safety / Ethics Notes (defensive framing)

- **Archived offensive tooling warning is explicit**: "Run it only in isolated environments, against systems you are explicitly authorised to test, and never as part of a production security programme." → the strongest statement in our study corpus of *why* staging + authorization matter.
- The project's funding rationale is defensive: an immune-system-for-robots goal required honestly measuring AI offense — "a defensive system can only be designed against an adversary whose real capability is known."
- The prompt-injection research (`CAIR-R01`) is directly adopted into the CyberSecurityEngineer agent's behavior: tool output is untrusted input.
- This study extracts **no** payloads, prompts, or operational technique detail; all evidence points at architecture docs, structural listings, and README governance text. → `SAFE-R08`

## 8) Observed Anti-Patterns (for our avoidance)

| Observation | Our counter-rule |
|---|---|
| `cli_headless.py` ~125 KB single module | Split subsystems before modules exceed reviewable size (`CAIR-R15`) |
| Dual `pricings/` trees (root + `src/cai/pricings/`) | One home per artifact (`SAFE-R10`) |
| License partition documented only in README prose | State partition at path level **and** consider per-file SPDX headers (`CAIR-R08` note) |
| Archived package remains installable with warnings limited to README/PyPI text | Keep the warning pattern; surface successor-pointer in package metadata too (`CAIR-R07` note) |

## 9) References

- Repository (archive): https://github.com/aliasrobotics/cai · default branch `archive`
- Framework paper: https://arxiv.org/abs/2504.06017 (CAI: An Open, Bug Bounty-Ready Cybersecurity AI)
- Guardrails research: https://arxiv.org/abs/2508.21669 (Hacking the AI Hackers via Prompt Injection)
- Benchmark paper: https://arxiv.org/abs/2510.24317 (CAIBench)
- Successor: https://aliasrobotics.com/cybersecuritysuperintelligence.php (CSI)
- Companion rules: `ACC/Reference/Upstream.Cai.Rules.md`
- Related studies: `Knowledge/PentestGpt/Study.md` (predecessor line), `Knowledge/AiPentestAgent/Study.md`, `Knowledge/JailbreakBench/Study.md`, `Knowledge/LlmAttacks/Study.md`

### Citation

```
@article{mayoral2025cai,
  title={CAI: An Open, Bug Bounty-Ready Cybersecurity AI},
  author={Mayoral-Vilches, V{\'\i}ctor and Navarrete-Lozano, Luis Javier and Sanz-G{\'o}mez, Mar{\'\i}a and Espejo, Lidia Salas and Crespo-{\'A}lvarez, Marti{\~n}o and Oca-Gonzalez, Francisco and Balassone, Francesco and Glera-Pic{\'o}n, Alfonso and Ayucar-Carbajo, Unai and Ruiz-Alcalde, Jon Ander and Rass, Stefan and Pinzger, Martin and Gil-Uriarte, Endika},
  journal={arXiv preprint arXiv:2504.06017},
  year={2025}
}
```
