# AIBreakRules — GitHub Copilot Integration

> This file points GitHub Copilot (and any agent that discovers `.github/copilot-instructions.md` automatically) to the repository's root bootstrap.

---

## Boot Instructions

1. **Read** [`../copilot-instructions.md`](../copilot-instructions.md) — the root bootstrap (SSOT chain head).
2. **Read** [`../manifest.json`](../manifest.json) — identity, language, paths, approvals.
3. **Read every file in** [`../ACC/Reference/`](../ACC/Reference/):
   - `Safety.md` — non-negotiable safety rules (SAFE-R01..R10)
   - `Output.md` — artifact quality standard (OUT-R01..R07)
   - `Manifest.md` — manifest interpretation rules
   - `Workflow.Index.md` — workflow registry
   - `Upstream.LlmAttacks.Rules.md` — extracted rules (LLMA-R01..R20)
   - `Upstream.AiPentestAgent.Rules.md` — extracted rules (AIPA-R01..R16)
   - `Upstream.PentestGpt.Rules.md` — extracted rules (PGPT-R01..R18)
   - `Upstream.JailbreakBench.Rules.md` — extracted rules (JBB-R01..R19)
   - `Upstream.Cai.Rules.md` — extracted rules (CAIR-R01..R16)
4. **Load `Knowledge/` only on demand** when a task cites a study.
5. **Resident agents:** [`agents/RuleSmith.agent.md`](agents/RuleSmith.agent.md) — repo studies, rule extraction, rule audits. [`agents/CyberSecurityEngineer.agent.md`](agents/CyberSecurityEngineer.agent.md) — cybersecurity engineering, security review, authorized testing. Docs: [`../docs/`](../docs/).

## Key Constraints (short form — full text in `ACC/Reference/Safety.md`)

- Quality > speed, always. No skeleton output.
- Read rules to EOF before acting; fail-fast on ambiguity.
- Secrets: environment variables only — never in files, logs, or commits.
- Git: no force-push, no `reset --hard`, no destructive history rewrites.
- Scope: defensive/educational only — no operational attack tooling or harmful content.
- Destructive actions require explicit human approval (`manifest.json → Approvals`).

## Language

Default response language: **Hinglish** (per `manifest.json → Developer.Language`).
