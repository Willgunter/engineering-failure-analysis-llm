# Evals

This directory contains the evaluation entry points used in this project, plus
saved outputs from past runs.

## Quick map

- `evals/custom-evals/` — simple runner for a small prompt set you manually review and score.
- `evals/eleuther-ai-evals/` — wrapper around EleutherAI `lm-eval-harness` for standardized benchmarks.
- `evals/eng-design/` — vLLM-based harness for EngiDesign OPEN tasks (JSON schema + task-native evaluators).

## Benchmarks used (typical)

- **EngiDesign OPEN** (task-native evaluators; see `evals/eng-design/README.md`)
- **EleutherAI lm-eval-harness tasks** (see `evals/eleuther-ai-evals/README.md`), commonly:
  - ARC-Challenge (`arc_challenge`)
  - GSM8K (`gsm8k`)
  - SVAMP (`svamp`)
  - MathQA (`mathqa`)
  - MMLU engineering-adjacent subsets, e.g.:
    - `mmlu_electrical_engineering`
    - `mmlu_college_computer_science`
    - `mmlu_college_physics`
    - `mmlu_conceptual_physics`
    - `mmlu_college_mathematics`

## Outputs

Where results land depends on the runner:

- `evals/custom-evals/run_custom_eval.py` writes timestamped runs under `evals/custom-evals/runs/`.
- `evals/eleuther-ai-evals/eleuther-ai-eval.py` writes a single JSON file you specify via `--output` (many example outputs are under `evals/eleuther-ai-evals/results/`).
- `evals/eng-design/engidesign_open_eval.py` writes per-model JSON summaries under `--output-dir`.

