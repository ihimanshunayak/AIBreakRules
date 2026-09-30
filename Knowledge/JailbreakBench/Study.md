# Study — JailbreakBench (`JailbreakBench/jailbreakbench`)

> **Source:** https://github.com/JailbreakBench/jailbreakbench · studied at commit `23dbdf6b19650521604456229bc1d9c4156c85c1` (main)
> **License:** MIT · **Paper:** Chao et al., "JailbreakBench: An Open Robustness Benchmark for Jailbreaking Large Language Models", NeurIPS 2024 Datasets and Benchmarks Track (arXiv 2404.01318) · **Dataset DOI:** 10.57967/hf/2540
> **Scope:** defensive/educational extraction of benchmark-governance, judge/evaluation, submission-integrity, and reproducibility rules (`SAFE-R08`). No jailbreak prompts, attack strings, or dataset entries are reproduced.
> **Extracted rules:** `ACC/Reference/Upstream.JailbreakBench.Rules.md` (JBB-R01..R19)

---

## 1) What The Repository Is

An open-source **robustness benchmark** for measuring how well LLM jailbreak attacks succeed and how well defenses mitigate them. It ships: a published behavior dataset (JBB-Behaviors — harmful + benign companion sets, sourced with documented provenance), a public leaderboard, a repository of submitted attack artifacts, an evaluation pipeline with automated judge components, a judges-comparison dataset (human labels vs multiple LLM judges), and submission processes for new attacks and defenses.

**Why it is worth studying here:** it is a *metrology* project — the discipline of measuring something faithfully. Its governance choices (DOI'd datasets, typed judges validated against human majority, consent-bearing submission forms, two-layer submission, one-harness-for-everyone) are the strongest available template for any evaluation claim this repository will ever make. The JBB rules are those choices, generalized past jailbreak benchmarking into "how to run and trust an evaluation".

## 2) Structure Map

```
jailbreakbench/
├── README.md                       ← benchmark overview, datasets, pipeline, submissions, judges, citation (JBB-R01..R19 evidence)
├── LICENSE (MIT) / CITATION.bib    ← citation artifact at repo root (JBB-R19)
├── CONTRIBUTING.md                 ← uv toolchain; format/lint/mypy/pytest gates; api_key marker (JBB-R17/R18)
├── pyproject.toml / uv.lock        ← locked dependency resolution (JBB-R16)
│
├── .github/
│   ├── ISSUE_TEMPLATE/attack-submission.yml  ← structured form + consent checkboxes (JBB-R11)
│   └── workflows/lint.yml + publish.yml      ← CI quality gates (JBB-R17)
│
├── src/jailbreakbench/             ← the package
│   ├── __init__.py                 ← public API surface (read_artifact, read_dataset, evaluate_prompts, create_submission, LLM wrappers)
│   ├── dataset.py                  ← dataset loading by name (JBB-R01)
│   ├── artifact.py                 ← reading submitted jailbreak artifacts + parameters metadata
│   ├── submission.py               ← evaluates prompts → generates submission package (JBB-R09)
│   ├── config.py
│   ├── classifier.py               ← judge components behind one ABC:
│   │                                  BaseClassifier → LlamaGuard1JailbreakJudge / Llama3JailbreakJudge /
│   │                                  Llama3RefusalJudge / StringClassifier            (JBB-R05/R06/R08)
│   ├── defenses/                   ← defense extension surface
│   │   ├── base_defense.py         ← Defense base contract; query(prompt) -> (response, int, int)  (JBB-R12)
│   │   ├── defenses_registry.py    ← DEFENSES dictionary registry
│   │   ├── erase_and_check.py / perplexity_filter.py / remove_non_dictionary.py /
│   │   │   smooth_llm.py / synonym_substitution.py
│   │   └── defenselib/             ← shared helpers (defense_hparams — hparams-driven, not hard-coded) (JBB-R10)
│   ├── llm/
│   │   ├── llm_wrapper.py          ← LLM contract (query with behavior + defense flags)
│   │   ├── litellm.py              ← API path (Together AI / OpenAI keys)              (JBB-R15)
│   │   ├── vllm.py / dummy_vllm.py ← local GPU path
│   │   └── llm_output.py           ← query logging (logs/dev/)
│   ├── plotting/                   ← result visualization (source breakdown)
│   └── vllm_server.py
│
├── examples/
│   ├── submit.py
│   └── prompts/llama2.json + vicuna.json   ← example artifact formatting fixtures
│
├── assets/                         ← figures used by README (dataset tables, breakdowns)
└── tests/                          ← unit tests: classifier, artifact, config; llm tiers (dummy/litellm/vllm) (JBB-R18)
```

## 3) Key Engineering Observations

1. **One judge interface, many judges** — `BaseClassifier.classify_responses(prompts, responses) -> list[bool]`; four concrete implementations swap without pipeline changes. → `JBB-R05`
2. **Judge determinism + grammar** — fixed positive-answer token, temperature `0`, explicit classification guidelines, addressed edge cases (affirmative-but-empty, irrelevant). → `JBB-R06`
3. **Mechanical degenerate-input guards** — responses under 15 words auto-classified non-successful, bypassing the LLM verdict. → `JBB-R08`
4. **Judges validated against human majority** — the judge-comparison dataset ships human labels + four LLM-judge labels over 300 examples. → `JBB-R07`
5. **Self-evaluation before submission** — submitters run `evaluate_prompts` themselves; the package they upload is generated by `create_submission` from that evaluation. → `JBB-R09`
6. **Hyperparameters as submission data** — `method_params` dictionaries travel with results; defenses read `self.hparams` instead of constants. → `JBB-R10`
7. **Extension by base contract + registry** — new defenses subclass `Defense`, implement `query`, register in `DEFENSES`. → `JBB-R12`
8. **Two-layer submission** — code PR merges first; artifacts follow, evaluated by the same harness. → `JBB-R13`
9. **Lockfile + CI gates** — `uv.lock` committed; `ruff format`/`ruff check`/`mypy`/`pytest` as the named gate (CI mirrors it). → `JBB-R16`, `JBB-R17`

## 4) Evaluation Methodology (as observed)

- **Catch-rate plus control set**: harmful behaviors paired with 100 thematically-similar benign behaviors, enabling false-positive measurement. → `JBB-R04`
- **Typed indicator matchers and structured score reports**: offenses/defenses scored via judges; results comparable across methods because everyone runs the same pipeline. → `JBB-R14`
- **Query-budget accounting**: submissions may report the number of queries their algorithm used (`phase="test"` query tracking). → `JBB-R09`
- **Judge configuration transparency**: judge model named in method params (e.g., `"judge-model": "jailbreakbench"`); per-judge accuracy is publicly measurable from the judges dataset. → `JBB-R07`, `JBB-R10`
- **Reproduction tiers**: API path (~$0.20/M tokens quoted) vs local vLLM path; both documented with setup. → `JBB-R15`

## 5) Reproducibility Notes (as documented upstream)

- Installable via `pip install jailbreakbench` (or editable local install); vLLM extra for local GPU runs; CUDA-compatible GPU required for local path.
- Datasets load by name from a versioned HuggingFace repository; DOI recorded in README.
- Judges dataset + behaviors dataset both carry stable loaders (`jbb.read_dataset(...)`, column schemas documented in README).
- Contributing docs state exact commands and the environment-variable prerequisites for the credentialed test tier. → `JBB-R18`
- Lockfile (`uv.lock`) committed at root. → `JBB-R16`

## 6) Safety / Ethics Notes (defensive framing)

- The project is explicitly for **tracking progress toward generating and defending against jailbreaks** — an academic robustness-measurement effort with peer-reviewed publication (NeurIPS D&B 2024).
- Dataset construction references **OpenAI's usage policies** as the public taxonomy anchor; provenance per behavior is tracked (Original / TDC-HarmBench / AdvBench). → `JBB-R03`
- Submission consent is explicit: contributors authorize MIT-licensed inclusion and retain copyright; forms enforce the upload of machine-generated submission archives. → `JBB-R11`
- This study extracts **no** jailbreak strings, prompts, responses, or dataset entries; all evidence points at governance files, interfaces, and README methodology text. → `SAFE-R08`
- Transferable defensive lessons for this repository's own future evaluation work: (a) never trust a judge you have not measured against human labels; (b) ship a benign control set with every harmful set; (c) make submissions self-evaluated, schema-validated, and consent-recorded; (d) lock the environment so numbers stay comparable.

## 7) Observed Anti-Patterns (for our avoidance)

| Observation | Our counter-rule |
|---|---|
| Deprecated `Classifier` class retained as a warning-bearing alias | Acceptable transitional pattern; prefer explicit deprecation marking (`PGPT-R16`) |
| Submission procedure restated in README while code lives in `submission.py` | Point docs at canonical code instead of restating logic (`LLMA-R03` spirit) |
| Default test path requires provider API keys | Mitigated by `-m "not api_key"` marker — generalized in `JBB-R18` |
| Very large `.gitignore` (7.7 KB) | Keep ignore rules categorized; audit for template leftovers |

## 8) References

- Repository: https://github.com/JailbreakBench/jailbreakbench
- Leaderboard: https://jailbreakbench.github.io
- Datasets: https://huggingface.co/datasets/JailbreakBench/JBB-Behaviors (DOI: 10.57967/hf/2540)
- Artifacts repository: https://github.com/JailbreakBench/artifacts
- Paper: https://arxiv.org/abs/2404.01318
- Companion rules: `ACC/Reference/Upstream.JailbreakBench.Rules.md`

### Citation

```
@inproceedings{chao2024jailbreakbench,
  title={JailbreakBench: An Open Robustness Benchmark for Jailbreaking Large Language Models},
  author={Patrick Chao and Edoardo Debenedetti and Alexander Robey and Maksym Andriushchenko and Francesco Croce and Vikash Sehwag and Edgar Dobriban and Nicolas Flammarion and George J. Pappas and Florian Tram{\`e}r and Hamed Hassani and Eric Wong},
  booktitle={NeurIPS Datasets and Benchmarks Track},
  year={2024}
}
```

Additional upstream-requested citation obligations (constituent datasets): AdvBench (`zou2023universal`) and TDC/HarmBench (`tdc2023`, `mazeika2024harmbench`) — full BibTeX blocks are in the upstream README §Citation and are preserved by reference.
