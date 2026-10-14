# Submission 193 Rebuttal Supplementary Materials

This repository accompanies our rebuttal to Reviewer 3 and holds materials that do not fit into a single OpenReview reply; it contains no information beyond the scope of the rebuttal's arguments.

| File | Supports | Content |
|---|---|---|
| W1_training_config.md | W1 (whether BMG directly predicts actual improvement) | Full training configuration and batch construction for the BMG prediction experiment and the BMG weight experiment, plus correlation statistics between BMG and improvement |
| W2_full_results.md | W2 (additional public domains) | Full results for all eight user simulators on the proprietary dataset, τ²-bench Retail, and Airline |
| W2_cross_family_results.md | W2 (different agent scales) | Full Retail and Airline results for SEAD and SimTeacher on Gemma-3-12B-it and Llama-3.1-8B-Instruct |
| W3_variance_and_agreement.md | W3 (multiple training runs, confidence intervals, rater agreement) | Confidence intervals and significance for the main results, the human evaluation protocol and agreement, and the variance of simulator quality |

Unless otherwise noted: τ²-bench results are mean ± sample standard deviation over three random training seeds (percentages); proprietary-dataset results are mean ± sample standard deviation over three rounds of human evaluation, with means identical to Tables 1 and 2 in the paper. "Improvements/leads" are in percentage points.
