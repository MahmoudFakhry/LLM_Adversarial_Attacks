# LLMs Under Attack: Comparing Adversarial Attacks on Large Language Models with Regularized Relaxation

This repository supports the research project:

> **LLMs Under Attack: Comparing Adversarial Attacks on Large Language Models with Regularized Relaxation**  
> Yuqing Liu, Mahmoud Fakhry, Hong Leng Toh  
> Oregon State University  
> **Paper:** `Paper/LLM_adversarial_attacks.pdf`

This work reproduces and extends prior RR (Regularized Relaxation) results and compares four adversarial methods for generating adversarial suffixes for LLMs: **PGD**, **RR (L2)**, **RR (L2+KL)**, and **BEAST**. Experiments measure attack success rate (ASR) on two benchmark sets (AdvBench, HarmBench) and explore transferability, readability, and practical limitations.

---

## Project Overview

- Replicate RR results and compare against PGD and BEAST.
- Evaluate on two adversarial datasets: **AdvBench** and **HarmBench** (50 prompts each).
- Train suffix generators on Llama2-7B-Chat, Falcon-7B-Instruct, MPT-7B-Chat.
- Evaluate transfer/ASR on Meta-Llama3-8B-Instruct and Beaver-7b-v1.0-cost.
- Main metric: **ASR** counted at two thresholds (ASR[>10], ASR[>5]).

---

## Motivation

Adversarial attacks can bypass safety filters and induce harmful outputs from LLMs. This work quantifies attack effectiveness, studies regularization to keep attacks semantically plausible, and compares gradient-based white-box methods to gradient-free black-box search (BEAST).

---

## Methods Evaluated

| Method | Type | Key idea |
|---|---:|---|
| PGD | White-box, gradient | Iterative embedding perturbation + nearest-neighbor discretization. |
| RR (L2) | White-box, gradient | Relax token space → continuous distributions + L2 toward vocab mean. |
| RR (L2 + KL) | White-box, gradient | Adds KL penalty on output distribution to preserve semantics/readability. |
| BEAST | Black-box, search | Beam-search + multinomial sampling to build adversarial suffixes (no gradients). |

---

## 🔍 Key Findings (concise)

- **PGD**: modest performance — **~16% (ASR[>10]) / 18% (ASR[>5])** overall (our runs).  
- **RR (L2)**: improved results — **~24% / 35%** overall.  
- **RR (L2+KL)**: further gains in some settings — **~26% / 30%** overall; better readability/transfer in HarmBench.  
- **BEAST**: strongest overall in this study — **34% (ASR[>10]) / 42% (ASR[>5])** (Table 3). Per-dataset: BEAST hit **24/50** on AdvBench for both thresholds.  
- Regularization towards the embedding manifold (L2) helps discretization and transfer; KL encourages readable, distribution-preserving attacks but is sensitive to hyperparameters.

---

## Technical Contributions

- Reproduction and extension of RR (adding KL term) and direct comparison with PGD & BEAST.
- Implementation details: discretization via nearest-neighbor lookup, L2 toward vocabulary mean, KL on output distributions.
- Empirical evaluation across multiple base models and two adversarial datasets.
- Practical notes on compute/memory trade-offs and regularizer selection.

---

## Experimental Insights & Limitations

- **Compute**: L2+KL increased VRAM & convergence time—A100 (40GB) required precision reduction; original used A6000 (larger VRAM).  
- **Sensitivity**: RR+KL is effective but **hyperparameter-sensitive**; tuning per model is costly.  
- **Magnet regularization** was tried but discarded due to conflict with L2 updates.  
- **BEAST**: more readable outputs and strong ASR, but much slower (beam search over large vocab). Choice of prefix and harmfulness scoring function is heuristic.  
- Current attacks are single-prompt; future work should explore multi-round / attention-aware attacks and improved readability benchmarks.

---

## Evaluation Metrics

- **Attack Success Rate (ASR)** — main metric, reported as counts/percent for ASR[>10] and ASR[>5].  
- **Transferability**, **query cost**, and **readability** (qualitative) were also considered.

---

## How to Use / Reproduce (short)

- Models & checkpoints: Llama2-7B-Chat, Falcon-7B-Instruct, MPT-7B-Chat (train); Meta-Llama3-8B-Instruct, Beaver-7b-v1.0-cost (eval).  
- Datasets: `AdvBench/` and `HarmBench/` (50 prompts each).  
- Implementations: `pgd/`, `rr_l2/`, `rr_l2_kl/`, `beast/` (train suffix embeddings or run beam search; discretize with nearest-neighbor to vocab).  
- Key hyperparameters: learning rate, λ (L2), β (KL), beam widths k1/k2, suffix length L.

---

## Future Work

- Multi-round dialogue attacks that leverage attention across turns.  
- Better, objective readability/stealth metrics.  
- Automated prefix selection and stronger black-box optimization strategies.  
- Integrate layered defenses and evaluate utility/robustness trade-offs.

---

## Acknowledgments

> Thanks to collaborators and datasets referenced in the paper.

---

## Citation

```bibtex
@misc{liu_fakhry_toh_llm_attacks,
  title={LLMs Under Attack: Comparing Adversarial Attacks on Large Language Models with Regularized Relaxation},
  author={Liu, Yuqing and Fakhry, Mahmoud and Toh, Hong Leng},
  year={2025},
  institution={Oregon State University},
  note={Paper/LLM_adversarial_attacks.pdf}
}
