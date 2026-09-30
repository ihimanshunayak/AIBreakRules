# ACC / Reference / Upstream — LlmAttacks Rules

> **Rules extracted from:** [`llm-attacks/llm-attacks`](https://github.com/llm-attacks/llm-attacks)
> **Source studied at:** commit `098262e` (main). **License:** MIT © 2023 Andy Zou — attribution preserved (`SAFE-R09`).
> **Scope note:** The upstream repository is adversarial-LLM *research code*. This document extracts **engineering, evaluation, and safety rules** for defensive/educational use (`SAFE-R08`). No operational attack content.
> Rule IDs (`LLMA-R*`) are stable — never reuse, never renumber.

---

## LLMA-R01 — Cite the paper, not just the code

**Statement:** Every project built on research must provide a ready-to-copy citation (paper + authors + year) in the README.
**Evidence:** `README.md` — "If you find this useful in your research, please consider citing:" + full BibTeX block (`zou2023universal`, arXiv 2307.15043).
**Why:** Without citation, derivative work loses its scholarly trail; users cannot trace provenance or credit.
**Applied:** This repo cites upstreams in `README.md` §3 + study docs; future AI/ML work must ship a Citation section.

---

## LLMA-R02 — License compliance with notice preservation

**Statement:** Ship a LICENSE file and preserve the upstream copyright notice in all copies/substantial portions.
**Evidence:** `LICENSE` — MIT, "Copyright (c) 2023 Andy Zou"; README badge "License: MIT".
**Why:** MIT requires the notice to travel with the code; violating it is a legal defect, not a style issue.
**Applied:** `LICENSE` (MIT) at repo root; upstream notices kept in study docs (`SAFE-R09`).

---

## LLMA-R03 — Pin exact versions of critical dependencies

**Statement:** Critical dependencies are pinned with `==` (exact), and the pin is stated in *both* the install docs and the dependency file — as one single source of truth.
**Evidence:** `requirements.txt` — `transformers==4.28.1`, `fschat==0.2.20`; `README.md` — "We need the newest version of FastChat `fschat==0.2.23` and please make sure to install this version."
**Why:** These libraries' tokenizers/APIs change behavior between releases; unpinned installs break silently. **Bonus lesson (derived):** README (`0.2.23`) and `requirements.txt` (`0.2.20`) disagree — proving the drift risk this rule exists to prevent. One version, one place.
**Applied:** Future LLM code in this repo pins exact versions and documents them in exactly one canonical location.

---

## LLMA-R04 — Match the model family your code was written for

**Statement:** Document the supported model families explicitly; refuse to run silently on unsupported ones.
**Evidence:** `README.md` Reproducibility — "Currently the codebase only supports training with LLaMA or Pythia based models. Running the scripts with other models (with different tokenizers) will likely result in silent errors."
**Why:** Tokenizer/special-token mismatches produce *silent* corruption — wrong slices, wrong losses, garbage results with no error.
**Applied:** Any model-dependent code documents its supported families + fails loudly (not silently) on others.

---

## LLMA-R05 — Centralize hyperparameters in a config object

**Statement:** All hyperparameters live in one config object (with defaults); change at the definition site or override at launch — never scattered across the codebase.
**Evidence:** `experiments/configs/template.py` (single `get_config()` returning full config); `experiments/configs/individual_*.py` (thin per-experiment overrides: `config.result_prefix = ...`); `experiments/main.py` (`_CONFIG = config_flags.DEFINE_config_file('config')`).
**Why:** Scattered constants make experiments unreproducible and reviews impossible; a config object makes every run self-describing.
**Applied:** Future experiments/code carry a single config definition + documented override mechanism.

---

## LLMA-R06 — One scripted entrypoint per scenario

**Statement:** Every scenario gets a named launch script with its exact command line — no "figure out the flags yourself".
**Evidence:** `experiments/launch_scripts/run_gcg_individual.sh` / `run_gcg_multiple.sh` / `run_gcg_transfer.sh` — each documents one scenario, fixes its flags, and loops data offsets (`for data_offset in 0 10 20 ... 90`).
**Why:** Reproducibility is operational: if the command exists in a script, anyone re-runs the experiment identically.
**Applied:** Registry in `Workflow.Index.md` gives every process a named invocation; future tooling gets `launch/` scripts.

---

## LLMA-R07 — Deterministic, timestamped output names

**Statement:** Output files embed a stable prefix + run timestamp; output directories are auto-created by the script.
**Evidence:** `experiments/main.py` — `timestamp = time.strftime("%Y%m%d-%H:%M:%S")`, `logfile=f"{params.result_prefix}_{timestamp}.json"`; launch scripts create `../results` if missing.
**Why:** Overwrites destroy evidence; timestamps make runs auditable and diffable.
**Applied:** Naming convention: `<prefix>_<YYYYmmdd-HHMMSS>.<ext>` for generated artifacts; never overwrite run outputs.

---

## LLMA-R08 — Incremental, machine-readable logging

**Statement:** Runs write structured (JSON) progress logs continuously — including current candidates/state — so a crashed run is diagnosable.
**Evidence:** `experiments/main.py` logs `controls` incrementally to the JSON logfile; `experiments/evaluate.py` loads `log['controls']` from a prior run and subsamples (`mini_step = len(controls) // 10`) to build evaluation checkpoints.
**Why:** Multi-hour GPU runs die; without incremental state, all evidence is lost.
**Applied:** Long-running processes log incrementally in structured form; evaluations can slice history.

---

## LLMA-R09 — Explicit, consistent data slicing

**Statement:** Dataset position/count/split parameters (`data_offset`, `n_train_data`, `n_test_data`) are explicit per run and consistent between related scripts.
**Evidence:** `experiments/configs/template.py` (`n_train_data = 50`, `n_test_data = 0`, `data_offset = 0`); launch scripts override per scenario; `api_experiments/evaluate_api_models.py` supports `n_instructions` as count **or** `"start:end"` range.
**Why:** Implicit slicing makes two runs incomparable; explicit slicing makes results reproducible and mergeable.
**Applied:** Any dataset-consuming process states its slice explicitly; no hidden sampling.

---

## LLMA-R10 — Secrets only from environment variables

**Statement:** API keys and credentials are read from environment variables at runtime — never stored in code or config files.
**Evidence:** `api_experiments/evaluate_api_models.py` — `openai.api_key = os.getenv("OPENAI_API_KEY")`, `ANTHROPIC_API_KEY = os.getenv("ANTHROPIC_API_KEY")`.
**Why:** Code/config travels; secrets must not. Env indirection is the only scalable discipline.
**Applied:** `SAFE-R05` (Secrets Discipline) — same rule, enforced repo-wide.

---

## LLMA-R11 — Explicit external-model allowlist

**Statement:** External model access goes through an explicit allowlist function; unknown models raise a clear error — never a silent fallback.
**Evidence:** `api_experiments/evaluate_api_models.py` — `load_chatbot()` checks `model_name in [...]` and otherwise `raise NotImplementedError(f"We do not have API keys for {model_name}.")`.
**Why:** Silent fallbacks produce misleading results; an allowlist keeps cost/keys/permissions auditable.
**Applied:** Any external API integration in this repo declares an allowlist + explicit failure.

---

## LLMA-R12 — Bounded retry with spacing for external calls

**Statement:** External API calls use retry with a fixed delay; transient failures never crash a long batch.
**Evidence:** `api_experiments/evaluate_api_models.py` — `while True: try: ... except: pass; time.sleep(10)` around chat calls.
**Why:** Long evaluation batches over flaky networks otherwise lose hours.
**Applied:** Applied as a pattern, **with a guard**: retries must be bounded (max attempts) + logged — unbounded silent retry loops are an anti-pattern this repo forbids (see §Anti-patterns).

---

## LLMA-R13 — Standardized evaluation criteria = reproducible scoring

**Statement:** Success/failure criteria are a documented, versioned list — not ad-hoc human judgment per run.
**Evidence:** `experiments/evaluate.py` — canonical `_test_prefixes` list (refusal markers such as "I'm sorry", "I cannot", ...); `api_experiments/evaluate_api_models.py` — keyword "checking" function + soft/hard pass rates recorded per prompt pair.
**Why:** If the criterion lives in a researcher's head, two runs are not comparable.
**Applied:** Any evaluation in this repo ships its criteria list + how scores are computed.

---

## LLMA-R14 — Benchmark dataset as versioned data files with fixed schema

**Statement:** Benchmarks ship as plain data files (CSV) in `data/`, with stable column structure and offsets — code consumes them by path, never by hardcoded inline samples.
**Evidence:** `data/advbench/harmful_behaviors.csv`, `data/advbench/harmful_strings.csv`, `data/transfer_expriment_behaviors.csv` — all consumed via `--config.train_data="../../data/advbench/..."`.
**Why:** Inline data can't be audited, diffed, or swapped; file-based datasets can.
**Applied:** Any dataset in this repo lives in versioned files with a documented schema.

---

## LLMA-R15 — Single source of truth for the version string

**Statement:** The package version is defined once (in the package `__init__`) and read by the build script — never duplicated.
**Evidence:** `setup.py` — `get_version('llm_attacks/__init__.py')` parses `__version__ = '0.0.1'`; `setup.py` raises `RuntimeError('Unable to find version string.')` if absent (fail-fast).
**Why:** Duplicated versions drift; reading one canonical value makes release bumps trivial and safe.
**Applied:** Aligns with `SAFE-R10` (Single Source of Truth) — one value, one home, fail-fast if missing.

---

## LLMA-R16 — Packaging via requirements.txt + `pip install -e .`

**Statement:** Dependencies live in `requirements.txt`; `setup.py` reads them into `install_requires`; dev install is editable (`-e .`) for reproducibility.
**Evidence:** `setup.py` — `install_requires=list(requirements.read().splitlines())`; `README.md` installation — `pip install -e .`.
**Why:** One dependency file feeds both pip and packaging → no second list to drift (`SAFE-R10`).
**Applied:** Future Python tooling in this repo follows the same single-list packaging pattern.

---

## LLMA-R17 — Instrumentation opt-in, experiments hermetic by default

**Statement:** External tracking (W&B etc.) is disabled by default in launch scripts; runs work offline.
**Evidence:** `experiments/launch_scripts/*.sh` — `export WANDB_MODE=disabled` as the first line of each script.
**Why:** Research runs must be hermetic; accidental tracker dependencies break reproducibility and leak metadata.
**Applied:** Side-effectful services (trackers, telemetry) are opt-in and off by default.

---

## LLMA-R18 — Document the reference hardware + known workarounds

**Statement:** Reproducibility section states the reference environment (hardware class) and links known-issue workarounds.
**Evidence:** `README.md` Reproducibility — "all experiments we run use one or multiple NVIDIA A100 GPUs, which have 80G memory per chip" + linked issue workarounds (Windows naming issue, GGML prompt format).
**Why:** "Works on my machine" is not reproducibility; hardware + workarounds are part of the method.
**Applied:** Any compute-heavy work in this repo documents minimum environment + pitfalls encountered.

---

## LLMA-R19 — Extension by explicit module registration, not code edits

**Statement:** New algorithm variants plug in as separately named modules selected by name; core code stays untouched.
**Evidence:** `experiments/main.py` — `attack_lib = dynamic_import(f'llm_attacks.{params.attack}')`; `llm_attacks/__init__.py` re-exports the stable public API (`AttackPrompt`, `PromptManager`, `MultiPromptAttack`, ...) that modules conform to.
**Why:** Editing core files per variant creates merge chaos; name-based dispatch keeps a stable kernel.
**Applied:** Future tooling: stable public API + name-selected extension modules.

---

## LLMA-R20 — Demo vs production separation, stated honestly

**Statement:** Minimal/demo implementations are clearly labeled "for familiarization only"; the real pipeline is separately documented and used for results.
**Evidence:** `README.md` Demo — minimal notebook "should be only used to get familiar with the attack algorithm. For running experiments with more behaviors, please check Section Experiments"; config template comment pattern keeps production values in `template.py`.
**Why:** Confusing demo code with production code produces invalid results and wasted effort.
**Applied:** `OUT-R01` — label every artifact honestly; demos never masquerade as the real pipeline.

---

## Anti-patterns observed / documented for avoidance

| Anti-pattern | Where it appears upstream | This repo's rule |
|---|---|---|
| Version drift between README and requirements | README `fschat==0.2.23` vs `requirements.txt` `0.2.20` | `LLMA-R03` — one version, one place |
| Unbounded silent retry (`while True: ... except: pass`) | `evaluate_api_models.py` | `LLMA-R12` — retry only if bounded + logged |
| One-off evaluation criteria living in code constants | `_test_prefixes` inline list | `LLMA-R13` — criteria as versioned artifacts |
| Off-by-one-ish config naming (`transfer_expriment_behaviors.csv` typo in filename) | `data/` folder | `OUT-R02` — filenames are API; typo-proof before ship |

---

## Citation (upstream)

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
