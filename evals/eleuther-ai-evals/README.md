# EleutherAI `lm-eval-harness` runs

This folder contains a small wrapper script around EleutherAI `lm-eval-harness`
plus a `results/` directory with example outputs.

- Runner: `evals/eleuther-ai-evals/eleuther-ai-eval.py`
- Example outputs: `evals/eleuther-ai-evals/results/`

## Quick start

Install dependencies in your environment (exact versions depend on your CUDA /
platform and on `lm-eval-harness` releases). Then run:

```bash
python evals/eleuther-ai-evals/eleuther-ai-eval.py \
  --models google/gemma-2-2b \
  --benchmarks arc_challenge,gsm8k \
  --batch-size 2 \
  --model-args dtype=float16,device_map=auto \
  --output results.json
```

## Caching on HPC / scratch

It’s often useful to point HF caches at local scratch:

```bash
cd /scratch/$SLURM_JOB_ID
mkdir -p run
cd run

export HF_HOME=$PWD/.hf
export HF_DATASETS_CACHE=$HF_HOME/datasets
export TRANSFORMERS_CACHE=$HF_HOME/transformers
```

## Colab (optional PyTorch install snippet)

If you need to force a specific CUDA build of PyTorch in Colab, you can use a
snippet like:

```bash
pip install -q --force-reinstall \
  torch==2.7.1+cu128 \
  torchvision==0.22.1+cu128 \
  torchaudio==2.7.1+cu128 \
  --extra-index-url https://download.pytorch.org/whl/cu128
```

## Notes / gotchas

- `--batch-size` controls how many prompts run in parallel per forward pass; bigger
  is faster but uses more VRAM. If you hit CUDA OOM, try `--batch-size 1`.
- For generative tasks (e.g. GSM8K), you can pass `--max-new-tokens` to control the
  generation budget (example: `--max-new-tokens 256`).
- Runs are typically **0-shot** (no in-context exemplars). The wrapper script does
  not pass `num_fewshot`, and `lm-eval-harness` defaults to 0 unless a task specifies
  otherwise.
- Some models (notably `ServiceNow-AI/Apriel-5B-Base`) have been sensitive to
  `transformers` / `lm-eval-harness` version combinations. The wrapper includes a
  small compatibility shim for `transformers.utils.LossKwargs`, but you may still
  need to adjust versions if you hit import errors.
- `mathqa` can be brittle across `datasets` / `lm-eval-harness` versions (e.g., dataset
  script deprecations). If it fails, try upgrading those packages or temporarily
  excluding the task.
