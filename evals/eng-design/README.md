# EngiDesign OPEN (vLLM harness)

This folder contains a vLLM-based evaluation harness for **EngiDesign OPEN**
tasks, where each task provides:

- `LLM_prompt.txt` — the task instructions/prompt
- `output_structure.py` — a Pydantic `Response_structure` describing the required JSON
- `evaluate.py` — an `evaluate_llm_response(response)` function that scores the output

The harness runs each prompt, extracts a JSON object, validates it against the
task schema, and executes the task’s evaluator in a subprocess.

## Dependencies (typical)

This harness is intended for GPU environments (Colab / workstation / HPC):

- `vllm`
- `torch`
- `transformers`

Individual tasks may require extra libraries (e.g. `numpy`, `scipy`, `opencv-python`),
depending on what their `evaluate.py` imports.

## What’s in here

- `build_engidesign_dataset.py` — scans the task directory and builds `engidesign_open_dataset.jsonl`
- `engidesign_open_eval.py` — main vLLM runner (one model at a time)
- `run_engidesign_eval.py` — helper that validates + calls task-native evaluators
- `engidesign_open_dataset.jsonl` — a generated (or checked-in) task index
- `experiment` — a scratch log of common failure modes observed during runs

## Task source (important)

The actual EngiDesign task folders (`EngDesign-Open/<task_id>/...`) are **not**
included in this repository. To run the dataset build step, you must point the
scripts at a local checkout of the task directory:

```bash
export ENGIDESIGN_ROOT=/path/to/EngDesign-Open
```

By default, `build_engidesign_dataset.py` looks for `evals/eng-design/EngDesign-Open/`.

## 1) Build / refresh the task index

From the repo root:

```bash
ENGIDESIGN_ROOT=/path/to/EngDesign-Open \
python evals/eng-design/build_engidesign_dataset.py
```

This writes `evals/eng-design/engidesign_open_dataset.jsonl`.

## 2) Run a smoke test

From the repo root:

```bash
python evals/eng-design/engidesign_open_eval.py \
  --dataset evals/eng-design/engidesign_open_dataset.jsonl \
  --models Qwen/Qwen2.5-3B-Instruct meta-llama/Llama-3.2-3B-Instruct \
  --max-samples 5 \
  --output-dir evals/eng-design/EngDesign-Results/smoke
```

## 3) Run a full sweep

Drop `--max-samples` (or increase it) and point `--output-dir` to a durable
location (e.g. scratch on HPC or a mounted Drive folder in Colab):

```bash
python evals/eng-design/engidesign_open_eval.py \
  --dataset evals/eng-design/engidesign_open_dataset.jsonl \
  --models Qwen/Qwen2.5-3B-Instruct meta-llama/Llama-3.2-3B-Instruct \
  --output-dir evals/eng-design/EngDesign-Results/full
```

## Model choice (base vs instruct)

EngiDesign OPEN requires **strictly valid JSON** that matches a Pydantic schema.
In practice, many base models frequently emit extra prose, code fences, or
otherwise-invalid JSON. For this benchmark it can be more informative to use
an instruction-tuned sibling primarily for *format compliance*.

Examples used in experiments:

- `Qwen/Qwen2.5-3B` → `Qwen/Qwen2.5-3B-Instruct`
- `Qwen/Qwen3-4B-Base` → `Qwen/Qwen3-4B-Instruct`
- `HuggingFaceTB/SmolLM3-3B-Base` → `HuggingFaceTB/SmolLM3-3B-Instruct`
- `ibm-granite/granite-4.0-micro-base` → `ibm-granite/granite-4.0-micro-instruct`
- `meta-llama/Llama-3.2-3B` → `meta-llama/Llama-3.2-3B-Instruct`
- `google/gemma-2-2b` → `google/gemma-2-2b-it`
- `deepseek-ai/deepseek-llm-7b-base` → `deepseek-ai/deepseek-llm-7b-chat`

## Common failure modes

- **Invalid JSON / validation failures**: the model outputs non-JSON text, extra keys,
  duplicate keys, or the wrong types. Mitigations: instruction-tuned models,
  `--temperature 0`, and keeping `--max-new-tokens` reasonable.
- **Prompt too long**: tasks that exceed the model context window are skipped by default
  (`--skip-long-prompts` is enabled unless you pass `--no-skip-long-prompts`).
