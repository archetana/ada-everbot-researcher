# Literature Review: Efficient Fine-tuning Methods for Large Language Models

## Overview
This review covers recent advances in parameter-efficient fine-tuning (PEFT) methods for LLMs, focusing on LoRA variants and related techniques from 2023-2026.

## Key Papers Identified

### Foundational Works
1. **QLoRA: Efficient Finetuning of Quantized LLMs** (2023)
   - Dettmers et al.
   - Enables fine-tuning 65B parameter models on single 48GB GPU
   - Combines 4-bit quantization with LoRA
   - Uses Double Quantization, Paged Optimizers, and 4-bit NormalFloat

2. **LoRA: Low-Rank Adaptation of Large Language Models** (Original)
   - Hu et al.
   - Introduces low-rank decomposition for efficient adaptation

### Recent Advances (2024-2026)
3. **Parameter-Efficient Fine-Tuning for Large Models: A Comprehensive Survey** (2024)
   - Provides taxonomy of PEFT methods
   - Covers adapter-based, prompt-based, and hybrid approaches

4. **TLoRA+: A Low-Rank Parameter-Efficient Fine-Tuning Method for Large Language Models** (2026)
   - Latest method (5 days old as of search)
   - Builds on AdaLoRA principles
   - Dynamic parameter allocation based on importance scores

5. **Efficient Fine-Tuning of Quantized Models via Adaptive Rank and Bitwidth** (2025)
   - Extends QLoRA with adaptive techniques
   - Addresses quantization error reduction

6. **LoRAFusion: Efficient LoRA Fine-Tuning for LLMs** (2025)
   - Optimizes computation for large token counts
   - Investigates two-step vs fused approaches

7. **ARD-LoRA: Dynamic Rank Allocation for Parameter-Efficient Fine-Tuning** (2025)
   - Achieves 99.3% of full fine-tuning performance with 0.32% parameters
   - 18.8% improvement in parameter efficiency over DoRA

8. **Accurate and Efficient Fine-Tuning of Quantized Large Language Models** (2024)
   - Compares various PEFT methods on MMLU benchmark
   - Evaluates BLoRA and other quantized approaches

9. **Evolution of Meta’s LLaMA Models and Parameter-Efficient Fine-Tuning: A Survey** (2025)
   - Comprehensive overview of LLaMA family and PEFT techniques
   - Analyzes cost/deployment trade-offs

## Research Gaps Identified
Based on preliminary review:

1. **Limited comparison across model families** - Most studies focus on LLaMA; limited work on Mistral, Phi, or Gemma families
2. **Insufficient evaluation on long-context tasks** - Few PEFT studies evaluate on tasks requiring >32k context
3. **Minimal investigation of interaction with RLHF** - How PEFT methods behave when combined with preference optimization
4. **Lack of theoretical understanding** - Limited work on why certain rank allocations work better
5. **Hardware-aware optimization scarce** - Few methods explicitly optimize for specific GPU architectures (Hopper, Blackwell)

## Potential Novel Contributions
1. **Hardware-aware adaptive PEFT** - Methods that adjust rank/bitwidth based on real-time hardware utilization
2. **Long-context PEFT specialization** - Techniques designed specifically for maintaining performance with extended contexts
3. **PEFT-RLHF interaction analysis** - Systematic study of how PEFT methods affect alignment and safety
4. **Cross-family PEFT transferability** - Understanding which PEFT hypertransfer across model architectures
5. **Unified framework for dynamic PEFT** - Combining rank adaptation, bitwidth adjustment, and importance scoring

## Next Steps
1. Retrieve full PDFs of top 5 most relevant papers
2. Create annotated bibliography with key findings
3. Implement baseline LoRA/QLoRA for comparison
4. Design experiments to address identified gaps
5. Draft related work section for paper

## References (to be expanded)
[1] Dettmers et al., "QLoRA: Efficient Finetuning of Quantized LLMs", 2023.
[2] Hu et al., "LoRA: Low-Rank Adaptation of Large Language Models", 2021.
[3] Zhang et al., "AdaLoRA: Adaptive Budget Allocation for Parameter-Efficient Fine-Tuning", 2023.
[4] Recent survey papers from 2024-2026 as identified above.

---
*Literature review initiated: 2026-04-19*
*Next update: Retrieve full papers and deepen analysis*