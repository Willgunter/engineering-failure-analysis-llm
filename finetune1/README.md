# Fine-tuning (LoRA / Unsloth)

This folder contains scripts and a Colab notebook used for a couple of
fine-tuning attempts on engineering failure-analysis style data.

The training datasets used during the original experiments are **not included**
in this repo (copyright/licensing constraints). You’ll need to supply your own
formatted text files.

## Dependencies (typical)

The scripts use Unsloth + Hugging Face Trainer. Exact installs vary by CUDA and
platform, but you will generally need: `unsloth`, `torch`, `transformers`,
`datasets`, and `peft` (plus a working GPU stack if you want speed).

## Data format expected by the scripts

Both scripts read a single text file containing multiple examples separated by:

```
---WILLGPTSTART---
```

- Attempt 1 treats each chunk as a **response-only** training example (document-style).
- Attempt 2 tries to parse each chunk as a **Q/A pair** containing `Q:` and `A:`; if
  it can’t, it falls back to response-only.

## Attempt 1 (document-style / “continued pretrain” shape)

Script: `finetune1/attempt1/finetune1_script.py`

```bash
python finetune1/attempt1/finetune1_script.py \
  --data-path /path/to/formatted_data.txt \
  --model Qwen/Qwen3-0.6B
```

Outputs:

- `./unsloth-qwen/` (trainer output dir)
- `./unsloth-qwen-lora/` (saved LoRA adapter + tokenizer)

## Attempt 2 (Q/A-style chunks)

Script: `finetune1/attempt2/finetune1_script_attempt2.py`

```bash
python finetune1/attempt2/finetune1_script_attempt2.py \
  --data-path /path/to/finetune1_formatted_data_attempt2.txt \
  --model Qwen/Qwen3-0.6B
```

Note: the script currently saves the adapter to `./qwen3-8B-extreme-reasoning-lora/`
regardless of the `--model` you choose; treat that as a historical name and
rename/edit as needed.

## Merging a LoRA adapter into a base model

Script: `finetune1/merge_adapters.py`

```bash
python finetune1/merge_adapters.py --model Qwen/Qwen3-0.6B
```

This script currently expects the adapter at `./unsloth-qwen-lora`. If you saved
adapters elsewhere, update the path in the script or move the folder.

## Notebook

`finetune1/finetune1.ipynb` is a Colab-oriented run log (mount Drive, install
deps, run training, etc.). Prefer the scripts for reproducibility.
