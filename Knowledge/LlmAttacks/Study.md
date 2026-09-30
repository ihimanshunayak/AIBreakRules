# Study — llm-attacks (`llm-attacks/llm-attacks`)

> **Source:** https://github.com/llm-attacks/llm-attacks · studied at commit `098262e` (main)
> **License:** MIT © 2023 Andy Zou · **Paper:** arXiv 2307.15043 (Zou et al., "Universal and Transferable Adversarial Attacks on Aligned Language Models")
> **Scope:** defensive/educational extraction of engineering rules (`SAFE-R08`). No operational attack content is reproduced.
> **Extracted rules:** `ACC/Reference/Upstream.LlmAttacks.Rules.md` (LLMA-R01..R20)

---

## 1) What The Repository Is

The official research codebase for the GCG (Greedy Coordinate Gradient) adversarial-prompt attack on aligned LLMs. It is a **research artifact**: a Python package (`llm_attacks`) + an experiment framework (`experiments/`) + benchmark data (`data/advbench/`) + an API-model evaluation harness (`api_experiments/`) + a teaching notebook (`demo.ipynb`).

**Why it is worth studying here:** independent of its subject matter, it is a clean example of a *research-grade ML codebase* — package layout, config discipline, launch scripts, evaluation harness, reproducibility documentation. Those engineering patterns transfer directly to any AI project. The rules extracted (LLMA-R01..R20) are exactly those patterns, plus safety/ethics observations.

## 2) Structure Map

```
llm-attacks/
├── LICENSE                     ← MIT, notice-preservation requirement (LLMA-R02)
├── README.md                   ← install/models/demo/experiments/reproducibility/citation
├── requirements.txt            ← pinned deps: transformers==4.28.1, fschat==0.2.20 (LLMA-R03)
├── setup.py                    ← version read from package __init__ (LLMA-R15); deps from requirements (LLMA-R16)
├── demo.ipynb                  ← minimal teaching implementation (LLMA-R20: demo ≠ production)
│
├── llm_attacks/                ← the importable package
│   ├── __init__.py             ← __version__ + stable public API re-exports (LLMA-R15/R19)
│   ├── base/attack_manager.py  ← core abstractions (AttackPrompt, PromptManager, MultiPromptAttack, ...)
│   ├── gcg/gcg_attack.py       ← the GCG algorithm module (selected by name — LLMA-R19)
│   └── minimal_gcg/            ← minimal variant (opt_utils.py, string_utils.py) for the demo
│
├── experiments/                ← the research harness
│   ├── main.py                 ← orchestrator: config → dynamic module import → attack.run() (LLMA-R19)
│   ├── configs/                ← template.py + thin per-model overrides (LLMA-R05)
│   │   ├── template.py         ← ALL hyperparameters in one config object (LLMA-R05)
│   │   ├── individual_{vicuna,llama2}.py
│   │   └── transfer_{vicuna,llama2,vicuna_guanaco}.py
│   ├── launch_scripts/         ← one script per scenario, exact flags fixed (LLMA-R06)
│   │   ├── run_gcg_individual.sh
│   │   ├── run_gcg_multiple.sh
│   │   └── run_gcg_transfer.sh
│   ├── evaluate.py             ← cross-model evaluation harness (LLMA-R13: _test_prefixes)
│   ├── evaluate_individual.py
│   └── parse_results.ipynb     ← results analysis
│
├── api_experiments/
│   └── evaluate_api_models.py  ← closed-API model evaluation:
│                                  allowlist dispatch (LLMA-R11), env-var keys (LLMA-R10),
│                                  retry loop (LLMA-R12), keyword checker + soft/hard rates (LLMA-R13)
│
└── data/
    └── advbench/               ← benchmark CSVs consumed by path (LLMA-R14)
        ├── harmful_behaviors.csv
        └── harmful_strings.csv
```

## 3) Key Engineering Observations

1. **Config-object pattern** — `experiments/configs/template.py` defines every hyperparameter in one `get_config()`; per-model files are ~10-line overrides. Launch scripts override at the CLI (`--config.n_steps=1000`). One definition site, many override points. → `LLMA-R05`
2. **Name-based module dispatch** — `dynamic_import(f'llm_attacks.{params.attack}')` selects the algorithm module at runtime; `__init__.py` re-exports the stable API surface. New variants plug in without editing core. → `LLMA-R19`
3. **Reproducible run identity** — outputs embed `result_prefix` + `%Y%m%d-%H:%M:%S` timestamp; results dir auto-created. → `LLMA-R07`
4. **Incremental evidence** — the attack loop logs candidates/controls into the JSON logfile as it progresses; evaluation loads and subsamples them. Crashed runs remain analyzable. → `LLMA-R08`
5. **Explicit data slicing** — `n_train_data`, `n_test_data`, `data_offset` are always explicit; individual runs loop offsets 0..90. → `LLMA-R09`
6. **Honest layering** — the minimal demo is labeled "for familiarization only"; production experiments live in `experiments/` with their own docs. → `LLMA-R20`

## 4) Evaluation Methodology (as observed)

- **Refusal detection** via a canonical prefix list (`_test_prefixes` in `evaluate.py`): "I'm sorry", "I cannot", "As an AI", ... — a run "passes" when no refusal prefix appears in the output.
- **Soft/hard scoring**: `soft_rate` = fraction of outputs passing; `hard_rate` = 1 if any passed. Both recorded per (prompt × instruction) pair, plus full outputs.
- **Cross-model results** saved as one JSON per evaluation run, keyed by model.
- **API models**: keyword checker with soft/hard rates; temperature `0` and `n=1` defaults in documented chat hparams.

*Why this matters as a rule:* criteria-as-versioned-artifact + scoring transparency = comparable runs. → `LLMA-R13`

## 5) Reproducibility Notes (as documented upstream)

- Reference hardware stated: NVIDIA A100 GPUs (80 GB/chip).
- Known-issue workarounds linked in README (Windows naming issue, GGML prompt format).
- Supported model families constrained to LLaMA/Pythia — other tokenizers "will likely result in silent errors", with a named tip for where to start adapting (slice definitions in `attack_manager.py`).
- `WANDB_MODE=disabled` exported in every launch script — online tracking off by default.

→ rules `LLMA-R04`, `LLMA-R17`, `LLMA-R18`

## 6) Safety / Ethics Notes (defensive framing)

- The upstream work is published peer-reviewed security research with a coordinated-disclosure posture (paper + website + model cards); studying its *engineering* does not require reproducing its *operational* content.
- Transferable defensive lessons: (a) refusal behavior is a measurable surface — so evaluation harnesses must exist for any alignment claim; (b) robustness questions deserve fixed, versioned criteria; (c) research artifacts should ship scoped, cited, and documented — which is exactly what this repo's rules enforce.
- This study extracts **no** prompts, payloads, or datasets. → `SAFE-R08`

## 7) Observed Anti-Patterns (for our avoidance)

| Observation | Our counter-rule |
|---|---|
| README (`fschat==0.2.23`) vs `requirements.txt` (`0.2.20`) version drift | `LLMA-R03` — one version, one home |
| Unbounded `while True` + bare-`except` retry around API calls | `LLMA-R12` — bound + log retries |
| Refusal-criteria list living inline in code | `LLMA-R13` — criteria as versioned artifacts |
| Filename typo shipped in data folder (`transfer_expriment_behaviors.csv`) | `OUT-R02` — filenames are API |

## 8) References

- Repository: https://github.com/llm-attacks/llm-attacks
- Paper: https://arxiv.org/abs/2307.15043
- Project site: https://llm-attacks.org/
- Extracted rules: `ACC/Reference/Upstream.LlmAttacks.Rules.md`

### Citation

```
@misc{zou2023universal,
      title={Universal and Transferable Adversarial Attacks on Aligned Language Models},
      author={Andy Zou and Zifan Wang and J. Zico Kolter and Matt Fredrikson},
      year={2023},
      eprint={2307.15043},
      archivePrefix={arXiv},
      primaryClass={cs.CL}
}
```
