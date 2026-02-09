# Engineering Failure Analysis LLM

Lightweight playground for adapting and evaluating small-to-mid sized open LLMs (≈2B–7B) toward engineering failure analysis / extreme-conditions reasoning.

The core behavior target is: given raw technical documentation, produce a
structured failure analysis grounded in first principles (explicit/implicit
assumptions, governing physics, interdependencies, failure modes, and safety
margins).

This repo is organized so the **root README stays high-level** and the folder READMEs carry the “how-to-run” details.

## Goals

- Start from strong open base models and adapt them for engineering-focused reasoning.
- Track progress with a repeatable evaluation suite (engineering knowledge + reasoning).
- Keep experiments easy to reproduce (scripts > notebooks where possible).

## Where to start

- Evals overview: `evals/README.md`
  - Custom manual prompt set: `evals/custom-evals/README.md`
  - Standard benchmarks (EleutherAI lm-eval-harness wrapper): `evals/eleuther-ai-evals/README.md`
  - EngiDesign OPEN (vLLM harness + task-native evaluators): `evals/eng-design/README.md`
- Fine-tuning scripts / notebook: `finetune1/README.md`
- Dataset curation notes + prompt template: `data/README.md`

## Base models (examples)

Starting points used in experiments (links go to Hugging Face model cards):

- [Qwen/Qwen2.5-3B](https://huggingface.co/Qwen/Qwen2.5-3B)
- [Qwen/Qwen3-4B-Base](https://huggingface.co/Qwen/Qwen3-4B-Base)
- [HuggingFaceTB/SmolLM3-3B-Base](https://huggingface.co/HuggingFaceTB/SmolLM3-3B-Base)
- [ServiceNow-AI/Apriel-5B-Base](https://huggingface.co/ServiceNow-AI/Apriel-5B-Base)
- [ibm-granite/granite-4.0-micro-base](https://huggingface.co/ibm-granite/granite-4.0-micro-base)
- [mistralai/Ministral-3-3B-Base-2512](https://huggingface.co/mistralai/Ministral-3-3B-Base-2512)
- [meta-llama/Llama-3.2-3B](https://huggingface.co/meta-llama/Llama-3.2-3B)
- [google/gemma-2-2b](https://huggingface.co/google/gemma-2-2b)
- [deepseek-ai/deepseek-llm-7b-base](https://huggingface.co/deepseek-ai/deepseek-llm-7b-base)

Notes on when/why instruct-tuned siblings were used (e.g., for strict JSON outputs) live under `evals/eng-design/README.md`.

## Data / licensing note

This repo intentionally does **not** include full-text articles or other potentially copyrighted corpora. Any derived datasets, model outputs, or summaries should be generated only from sources you have the rights to use; see `data/README.md`.

## Project status

Research code / experiment log: expect sharp edges (dependency churn, JSON compliance issues on schema-based tasks, etc.). Folder READMEs call out known issues and practical workarounds.
# engineering-failure-analysis-llm
