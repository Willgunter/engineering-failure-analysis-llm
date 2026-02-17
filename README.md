# Engineering Failure-Grounded Reasoning LLM

Structured supervision to shift LLM reasoning toward concise, constraint-focused engineering analysis.

## Live Demo

- HuggingFace Space: https://huggingface.co/spaces/Willgunter/Engineering-Reasoning-LLM

## Demo Videos

- [https://github.com/user-attachments/assets/ad2a70fe-2efd-4867-9fc5-b](https://github.com/user-attachments/assets/2840ed97-784e-4996-8c92-ae336b4f5951)


- https://github.com/user-attachments/assets/2840ed97-784e-4996-8c92-ae336b4f5951
- https://github.com/user-attachments/assets/d7ecf97b-e8fa-474e-bb8f-9ac182169dde
- https://github.com/user-attachments/assets/e117e9c7-11cc-4a95-9e22-95e8115ffba1

## Problem

Modern LLMs are often overly verbose, overly agreeable, and prone to drifting off topic when answering engineering questions. In high-stakes engineering contexts, unnecessary expansion and speculative commentary can obscure the true root constraint of a system.

Engineers need:

- Clear isolation of governing constraints
- First-principles reasoning
- Concise, high-signal responses
- Structured failure analysis

This project explores whether training on structured, real-world engineering failure analyses can shift an LLM's reasoning style toward constraint-focused outputs.

## Approach

### Dataset Design

- 200 real engineering analyses manually selected
- Domains included:
  - Extreme thermal systems
  - Civil infrastructure (bridges, tunnels, oil wells)
  - Catastrophic failures (e.g., Challenger, SpaceX incidents)
  - High-pressure and extreme-environment systems

Initial attempt: Direct long-context infusion of full engineering texts into fine-tuning produced incoherent, unstable outputs.

Pivot: Each analysis was decomposed into ~800 structured Q&A pairs.

Format: Each answer enforced 4-5 concise bullet points isolating core failure mechanisms, assumptions, and constraint breakdowns.

Key insight: Clear structure and constraint emphasis mattered more than raw domain exposure.

### Training

- Base model: Qwen 3 8B
- Method: Supervised Fine-Tuning (SFT) with QLoRA

LoRA rank experiments showed that low rank lead to minimal behavioral change, a higher rank lead to a clear shift towards structured constraint-focused reasoning, and they also showed training loss correlated with observed reasoning shift with an overarching goal of a change in reasoning style.

### Behavioral Shift (fine tuned output is in evals/custom-evals/results)

Compared to the base model, the fine-tuned model:

- Reduced token count significantly
- Stopped rephrasing or expanding prompts unnecessarily
- Avoided speculative tangents
- More consistently identified governing physical constraints
- Reduced reinforcement of flawed assumptions

On 15 custom engineering failure benchmarks:

- ~50 percent showed meaningful improvement in root cause identification
- Signal density increased
- Responses were more structured and constraint-driven

Verbosity was measured using token count comparison, and qualitative evaluation focused on correct constraint prioritization and failure mode identification.

## Evaluation

Custom benchmarks tested reasoning across domains including:

- Brittleness under extreme cold
- Crack propagation under pressure
- Thermal runaway
- Structural failure cascades
- Constraint prioritization in coupled systems

Evaluation criteria included:

- Correct root cause identification
- Reduction of irrelevant detail
- Structural adherence
- Conciseness (token count comparison)

Evaluation was manual and domain-specific, using targeted expectations per prompt.

## Deployment

A 0.6B 4-bit GGUF quantized variant is deployed on HuggingFace Spaces for demonstration.

Deployment is optimized for accessibility and reproducibility, not production-scale performance.

## Limitations

- Dataset size is relatively small (~800 Q&A pairs)
- Evaluation is manual and limited in scope
- No cross-domain generalization testing yet
- Improvements may reflect structured style imitation rather than deeper reasoning gains

This project should be viewed as a proof-of-concept exploration of reasoning style steering via structured domain supervision.

## Repository Structure

Detailed experiment logs and reproducibility instructions are organized in subfolders:

- Evaluation suite + results: `evals/`
- Fine-tuning scripts: `finetune1/`
- Dataset curation notes: `data/`

This root README focuses on the high-level reasoning intervention and findings.
