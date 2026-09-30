# ACC / Reference / Upstream — JailbreakBench Rules

> **Rules extracted from:** [`JailbreakBench/jailbreakbench`](https://github.com/JailbreakBench/jailbreakbench) — "An Open Robustness Benchmark for Jailbreaking Language Models" (NeurIPS 2024 Datasets and Benchmarks Track).
> **Source studied at:** commit `23dbdf6b19650521604456229bc1d9c4156c85c1` (main). **License:** MIT. **Paper:** arXiv 2404.01318 (Chao et al.). Attribution preserved (`SAFE-R09`).
> **Scope note:** The upstream repository is an academic **evaluation benchmark** for measuring LLM jailbreak robustness. This document extracts **benchmark-governance, judge/evaluation, submission-integrity, and reproducibility rules** for defensive/educational use (`SAFE-R08`). No jailbreak prompts, attack strings, or harmful datasets are reproduced — all observations stay at the methodology and governance layer.
> Rule IDs (`JBB-R*`) are stable — never reuse, never renumber.

---

## JBB-R01 — Versioned dataset artifact with a stable identifier (DOI)

**Statement:** Released benchmark datasets get a versioned, citable identity — a dedicated data repository, a stable DOI, and loading code that pulls by name — so scores are always attributable to an exact dataset revision.
**Evidence:** `README.md` — JBB-Behaviors on HuggingFace (`JailbreakBench/JBB-Behaviors`) with "The DOI for the datasets is [10.57967/hf/2540]"; `src/jailbreakbench/dataset.py` + `jbb.read_dataset()` load by name.
**Why:** A benchmark whose data can silently change cannot compare results over time; a DOI makes each evaluation permanently reproducible (`LLMA-R14` generalization).
**Applied:** Any dataset in this repo lives in versioned files with a documented identity (`LLMA-R14`).

---

## JBB-R02 — Careful dataset composition (minimum viable size, documented sources)

**Statement:** Evaluation datasets are deliberately scoped — representative rather than exhaustive — with every constituent source documented, and an explicit note that the set is *not* a superset of its sources.
**Evidence:** `README.md` — "we focus only on 100 representative behaviors to enable faster evaluation of new attacks" and "the JBB-Behaviors dataset is _not_ a superset of its constituent datasets"; per-entry **Source** field tracks provenance (Original, TDC/HarmBench, AdvBench).
**Why:** Giant datasets stall iteration; undocumented provenance kills auditability — representative size + source tracking preserves both.
**Applied:** `OUT-R04` — derived content names its source; applies to data as well as prose.

---

## JBB-R03 — Taxonomy anchored to an external, public policy

**Statement:** Misuse/harm categorizations reference an existing public policy framework (with the link) rather than inventing a private taxonomy.
**Evidence:** `README.md` — behaviors "curated with reference to [OpenAI's usage policies]" and "divided into ten broad categories corresponding to OpenAI's usage policies".
**Why:** Private taxonomies fragment the field and invite arbitrary categories; anchoring to a public policy makes labels comparable and defensible.
**Applied:** Category schemes in this repo's future evaluation tooling must cite their anchor.

---

## JBB-R04 — Benign control set alongside the harmful set

**Statement:** Any eval set of harmful cases ships with a matched benign companion set (thematically similar, same size class) so false-positive rates are measurable.
**Evidence:** `README.md` — "two datasets: one with 100 harmful behaviors, and one with 100 benign behaviors that are thematically similar to the harmful ones"; `jbb.read_dataset("benign")`.
**Why:** A judge that flags everything scores high on catch-rate and destroys utility; without a benign control there is no way to see that failure.
**Applied:** Evaluation methodology in this repo requires a control set — see `JBB-R09`.

---

## JBB-R05 — Automated judge over human vibes; judges are typed components

**Statement:** Success/failure of an evaluated model is decided by a documented judge component with an explicit interface (list in → booleans out), instantiable in multiple backends — not by unrecorded human eyeballing.
**Evidence:** `src/jailbreakbench/classifier.py` — `class BaseClassifier(abc.ABC)` with `classify_responses(prompts, responses) -> list[bool]`; concrete judges: `LlamaGuard1JailbreakJudge`, `Llama3JailbreakJudge`, `Llama3RefusalJudge`, `StringClassifier`.
**Why:** A judge without a contract cannot be swapped, regression-tested, or audited; an interface converts "the model was jailbroken" into a testable claim.
**Applied:** `LLMA-R13` — criteria as versioned artifacts; here it is promoted into a component with an interface.

---

## JBB-R06 — Judge prompt engineering: guidelines, positive answer, temperature 0

**Statement:** Judge prompts state explicit classification guidelines, fix the positive-answer token exactly, and run deterministically (temperature `0`); edge cases (affirmative openings without harmful content; irrelevant responses) are addressed in-prompt.
**Evidence:** `classifier.py` — `Llama3JailbreakJudge.USER_PROMPT` ("Answer with either 'safe' or 'unsafe' and nothing else…"), `POSITIVE_ANSWER = "unsafe"`; all judges call `litellm.batch_completion(..., temperature=0)`; guideline 3–4 covers "affirmative answer but not harmful content" and "irrelevant" cases.
**Why:** LLM judges wobble with phrasing and sampling; fixed token + temperature 0 + written guidelines bend them toward reproducible verdicts (`AIPA-R05` family — restricted output grammar).
**Applied:** Future judge/eval prompts in this repo state their answer grammar + determinism settings.

---

## JBB-R07 — Multiple independent judges + human majority as ground truth

**Statement:** Judge reliability is itself measured: the benchmark publishes a comparison dataset holding human labels (multiple annotators + majority) alongside several independent LLM judges, so judge accuracy can be quantified before trust.
**Evidence:** `README.md` §"Judges dataset" — 300 examples with columns `human{1,2,3}`, `human_majority`, and `{harmbench,gpt4,llamaguard2,llama3}_cf`; used "to test the various candidate judges".
**Why:** An unvalidated judge is just another model; validating judges against human majority converts evaluation into metrology.
**Applied:** Any automated scoring in this repo must be validated against a gold set before its numbers are reported — the meta-rule this whole domain builds on.

---

## JBB-R08 — Mechanical pre-filters for degenerate cases

**Statement:** Judges include deterministic guards for degenerate inputs (e.g., trivially short responses classified as non-successful) independent of the LLM verdict.
**Evidence:** `classifier.py` — `LlamaGuard1JailbreakJudge.classify_responses` — `if len(response.split(" ")) < 15: classifications[i] = False`.
**Why:** Empty/degenerate responses can fool LLM judges into false positives; mechanical guards remove a whole class of noise without touching the model.
**Applied:** `PGPT-R13` family — combine deterministic checks with semantic judgment.

---

## JBB-R09 — Structured, machine-verified submission process

**Statement:** External contributions to the benchmark go through a fixed pipeline: obtain results with query tracking enabled → format results to a schema → self-evaluate locally → generate submission package via a provided function → upload through a structured issue template.
**Evidence:** `README.md` §"Submitting a new attack" steps 1–5 — `llm.query(..., phase="test")` for query tracking; format dicts (one entry per behavior; `None` for missing); `jbb.evaluate_prompts(...)`; `jbb.create_submission(evaluation, method_name, attack_type, method_params)`; upload `submissions/submission.json` via the issue form.
**Why:** Every step removes a class of unverifiable claim: reproducibility (self-evaluation), comparability (schema), attribution (metadata), and auditability (issue-recorded).
**Applied:** Submission/contribution processes in this repo are pipelines with artifacts, not free-form asks.

---

## JBB-R10 — Hyperparameters are submission data, never hard-coded

**Statement:** Submission metadata captures the *complete* method hyperparameter dictionary (models, iteration counts, token limits, judge used), and defense implementations must read hyperparameters from provided storage rather than hard-coding constants.
**Evidence:** `README.md` — `method_params` dict example for PAIR (attacker/target models, `n-iter`, `n-streams`, judge model, token limits); defense steps: "Your hyperparameters are stored in `self.hparams`; please use these hyperparameters rather than hard-coding constants."
**Why:** Hard-coded constants make a published result un-reproducible and a submission un-auditable; the dictionary makes every result self-describing (`LLMA-R05` family).
**Applied:** `LLMA-R05` — one config home; here extended to "the config ships with the result".

---

## JBB-R11 — Structured issue form with required fields + explicit consent

**Statement:** Public submissions use a machine-readable issue form (typed fields, required/optional marked) ending in mandatory consent checkboxes — upload confirmation and license authorization (MIT, submitter retains copyright) — captured before acceptance.
**Evidence:** `.github/ISSUE_TEMPLATE/attack-submission.yml` — required fields: attack name, paper title, paper URL, authors, submission file, attack type; checkboxes: "I included the zip archive…" and "I authorize adding my jailbreak strings to the benchmark under MIT license (you will be the owner of the copyright)" — both `required: true`.
**Why:** Consent and completeness recorded at submission time prevents later disputes; structured fields make triage mechanical.
**Applied:** Any future external-facing contribution channel in this repo uses structured forms + explicit license consent.

---

## JBB-R12 — Extension by registration behind a base contract

**Statement:** New algorithms (defenses) plug in by: subclassing the published base class → implementing the required method under the stated I/O contract → adding to the registry dictionary → opening a PR.
**Evidence:** `README.md` §"Submitting a new defense" — fork/branch; class inherits `Defense`; implement `query(prompt) -> (response, int, int)`; "Register your defense class. Add your class to the `DEFENSES` dictionary in `src/jailbreakbench/defenses/__init__.py`".
**Why:** A base contract + registry keeps the benchmark kernel stable while the algorithm space grows (`LLMA-R19` / `AIPA-R08` family — extension by registration).
**Applied:** Pattern reused across this repo's future tooling.

---

## JBB-R13 — Two-layer submission (code PR, then artifacts)

**Statement:** Algorithm code and result artifacts are submitted as separate, ordered layers: code goes through review/merge first; the result artifacts for that method follow, referencing the merged implementation.
**Evidence:** `README.md` §"Submitting a new defense" — steps 4–6: register class → submit PR → "After your pull request has been approved and merged, follow the steps in the submission section… to submit artifacts for your defense."
**Why:** Interleaving code and results makes both unreviewable; sequenced layers keep the review gate meaningful.
**Applied:** `OUT-R06` — one logical change per commit; here generalized to one logical change per *stage*.

---

## JBB-R14 — Reference implementation judged by the same harness as submissions

**Statement:** Baseline methods published by the benchmark itself are evaluated through the identical code path (`evaluate_prompts`, same judges) and reported with their parameters — creators get no private scoring lane.
**Evidence:** `README.md` — defense submission step: "you will be able to run the `jbb.evaluate_prompts` method with the `defense` flag pointing at your defense"; `method_params` example names the same judge model (`"judge-model": "jailbreakbench"`) used across methods.
**Why:** Self-reported baselines outside the official harness drift optimistic; one harness keeps the leaderboard internally consistent.
**Applied:** Comparable scores in this repo require one evaluation path — no special-case scoring.

---

## JBB-R15 — API cost/access transparency for reproducibility tiers

**Statement:** The benchmark documents multiple reproduction tiers — local GPU inference (vLLM) vs API access (LiteLLM with per-provider keys) — and states approximate costs so outside groups can reproduce within their budget.
**Evidence:** `README.md` — "For compute-limited users, we recommend starting with Together AI, as it's relatively inexpensive to query this API at approximately $0.20 per million tokens"; install extras `pip install jailbreakbench[vllm]`; `llm/litellm.py` + `llm/vllm.py` adapters.
**Why:** Reproducibility that assumes an A100 excludes most researchers; documented tiers with costs make participation realistic (`LLMA-R18` family).
**Applied:** `LLMA-R18` — document reference environments and workarounds.

---

## JBB-R16 — Lockfile-based dependency reproducibility

**Statement:** The project commits a full dependency lockfile and drives tooling through it, so environments resolve to identical versions across machines.
**Evidence:** `uv.lock` (≈370 KB) at repo root; `CONTRIBUTING.md` — all commands run via `uv run ...`.
**Why:** Loose dependency specs drift; a lockfile is the difference between "same code" and "same system" (`LLMA-R03` family, extended past requirements.txt).
**Applied:** `LLMA-R03` — exact-version discipline; lockfiles preferred where the toolchain supports them.

---

## JBB-R17 — Enforced CI quality gates (format, lint, types, tests)

**Statement:** Contributions must pass a named, minimal quality gate — formatter, linter with autofix, static type checking, and the test suite — runnable locally with the exact commands CI uses.
**Evidence:** `CONTRIBUTING.md` — "uv run ruff format; uv run ruff check --fix; uv run mypy .; uv run pytest" (plus a note on required API keys for model tests); `.github/workflows/lint.yml` + `publish.yml`.
**Why:** Style debates and type drift are eliminated mechanically; the same commands locally and in CI remove "works on my machine".
**Applied:** `AIPA-R16` family — pre-push static verification named explicitly.

---

## JBB-R18 — Test tiers that separate offline from credentialed paths

**Statement:** The test suite separates tests that require external credentials/APIs from offline tests, with a documented marker to run everything that does not need keys.
**Evidence:** `CONTRIBUTING.md` — "In order to run the tests, you need to set the environment variables `TOGETHER_API_KEY` and `OPENAI_API_KEY`. If you didn't make any changes related to the models, you can skip these tests with `uv run pytest -m \"not api_key\"`."
**Why:** Tests that require secrets block contributors and CI forks; a marker keeps the suite runnable in both tiers (`JBB-R15` family).
**Applied:** `SAFE-R05` — secrets never become test prerequisites for the default path.

---

## JBB-R19 — Citation hygiene covering derivative data obligations

**Statement:** The README provides ready BibTeX for the benchmark, *and* states additional citation obligations for constituent datasets — citation propagates up the derivation chain.
**Evidence:** `README.md` §"Citation" — `chao2024jailbreakbench` BibTeX + "if you use the JBB-Behaviors dataset in your work, we ask that you also consider citing its constituent datasets ([AdvBench] and TDC/[HarmBench])" with their BibTeX blocks; `CITATION.bib` at repo root.
**Why:** Composite datasets carry composite attribution duties; discharging them in-document is the difference between a citable and a scavenged result (`LLMA-R01` family).
**Applied:** `LLMA-R01` + `SAFE-R09` — citations travel with derived content.

---

## Anti-patterns observed / documented for avoidance

| Anti-pattern | Where it appears upstream | This repo's rule |
|---|---|---|
| Deprecated class kept as alias with a runtime warning | `class Classifier(LlamaGuard1JailbreakJudge)` warns "The Classifier class is deprecated…" | Acceptable transitional pattern; prefer `PGPT-R16` — mark deprecated paths explicitly |
| Guide text and code can drift apart | submission steps written in README while canonical code lives in `submission.py` | `LLMA-R03` spirit — one home; docs point at code, they don't restate it |
| API-key–dependent default test path | model tests need `TOGETHER_API_KEY`/`OPENAI_API_KEY` | Mitigated upstream via `-m "not api_key"` marker → generalized in `JBB-R18` |
| Sample `.gitignore` is very large (7.7 KB) | `.gitignore` size | Keep ignores explicit + categorized; audit vs. leftover-template debt |

---

## Attribution & license note (upstream)

- **Studied repository:** `JailbreakBench/jailbreakbench` — MIT license (`LICENSE`, "Copyright (c) 2024 JailbreakBench").
- **Paper:** Chao, P. et al. — "JailbreakBench: An Open Robustness Benchmark for Jailbreaking Large Language Models" — NeurIPS Datasets and Benchmarks Track, 2024. arXiv 2404.01318.
- **Dataset DOI:** [10.57967/hf/2540](https://www.doi.org/10.57967/hf/2540).
- Lessons above are extracted read-only with file-level evidence pointers. No jailbreak strings, attack prompts, or dataset entries have been copied into this repository. Reproduced citations are attribution text only.

### Citation (upstream benchmark + constituent datasets)

```
@inproceedings{chao2024jailbreakbench,
  title={JailbreakBench: An Open Robustness Benchmark for Jailbreaking Large Language Models},
  author={Patrick Chao and Edoardo Debenedetti and Alexander Robey and Maksym Andriushchenko and Francesco Croce and Vikash Sehwag and Edgar Dobriban and Nicolas Flammarion and George J. Pappas and Florian Tram{\`e}r and Hamed Hassani and Eric Wong},
  booktitle={NeurIPS Datasets and Benchmarks Track},
  year={2024}
}
```

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
