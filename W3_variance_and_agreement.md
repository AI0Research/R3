# Confidence Intervals, Rater Agreement, and Variance of Simulator Quality

This file corresponds to our reply on W3 (multiple full training runs, confidence intervals, human rater agreement), giving the full statistics for all three parts.

## 1. Multiple Runs and Confidence Intervals for the Main Results

**Notes.** On τ²-bench, each user simulator is fully trained three times with 3 random seeds (42 / 1234 / 2026); the standard deviations in the table are sample standard deviations across the three full training runs (the initial agent has no training randomness; its entries come from three evaluations). The proprietary dataset follows the evaluation protocol of Appendix C in the paper: each method is trained once, and each of the 168 test tasks is completed in three rounds of interaction by real humans; the standard deviation is the sample standard deviation across the three rounds. Every evaluation round requires human participation (504 human sessions per method), so the cost cannot support 10 trials per task as on τ²-bench. Confidence intervals are computed with the t distribution (n = 3, 2 degrees of freedom); p values are from two-sided Welch t tests between SimTeacher and each method.

| User simulator | Proprietary dataset accuracy | 95% CI | SimTeacher lead | p value |
|---|---:|---:|---:|---:|
| — (initial agent) | 55.4±1.8 | [50.9, 59.9] | +29.3 | <0.001 |
| Qwen3-235B | 65.1±2.1 | [59.9, 70.3] | +19.6 | <0.001 |
| Gemini-2.5 | 63.3±1.7 | [59.1, 67.5] | +21.4 | <0.001 |
| DeepSeek-R1 | 66.5±1.9 | [61.8, 71.2] | +18.2 | <0.001 |
| USP-8B | 58.1±2.5 | [51.9, 64.3] | +26.6 | <0.001 |
| SEAD | 71.0±2.4 | [65.0, 77.0] | +13.7 | 0.002 |
| SFT SimTeacher | 75.6±2.1 | [70.4, 80.8] | +9.1 | 0.005 |
| SimTeacher | 84.7±1.5 | [81.0, 88.4] | — | — |

Multi-seed results for each method on τ²-bench are in `W2_full_results.md`.

## 2. Human Rater Agreement

**Evaluation protocol.** The proprietary-dataset evaluation is carried out by 24 real e-commerce users recruited and paid through a third-party vendor. After completing a multi-turn interaction with the agent, each user rates the service outcome on the five-point scale of Appendix C in the paper (scores of 4–5 count as correct), with three rounds per task. The agent's identity is hidden from users during evaluation; agent presentation order is randomized and balanced across users; all users are unrelated to this project and its deployment; the evaluation is conducted offline, separately from online feedback; and the test tasks take no part in any training or validation.

**Agreement measurement.** Since each interaction is rated by the user who completed it, we additionally asked three real users to independently and blindly re-rate, under the same criteria, a stratified random sample of 336 completed dialogues. Stratification is by "method × evaluation round", ensuring equal sample counts per method.

| Metric | Value |
|---|---:|
| Sampled dialogues | 336 |
| Krippendorff's α (ordinal, five-point) | 0.86 |
| Krippendorff's α (binary success, 4–5 vs. 1–3) | 0.81 |

Both α values exceed the commonly used 0.8 reliability standard.

## 3. Variance of the Simulator Quality Results

Mean ± sample standard deviation over three evaluations for the three metrics in Table 2 of the paper; evaluation tasks and protocol are the same as in the paper, with means identical to Table 2.

| User simulator | Naturalness | Persona adherence | Intent adherence |
|---|---:|---:|---:|
| Qwen3-235B | **94.3±1.0** | **96.0±1.1** | **98.0±0.4** |
| Gemini-2.5 | 93.5±1.8 | 94.3±1.9 | 96.5±0.7 |
| DeepSeek-R1 | **94.3±2.0** | 87.0±1.3 | 95.8±1.0 |
| USP-8B | 76.8±2.1 | 76.5±1.9 | 81.8±1.6 |
| SEAD | 75.5±1.8 | 77.0±1.5 | 92.8±1.2 |
| SFT SimTeacher | 92.8±1.4 | 87.3±1.3 | 81.3±1.8 |
| SimTeacher | 91.5±1.6 | 94.0±1.5 | 84.8±2.1 |

Changes of SimTeacher relative to SFT SimTeacher and SEAD (Welch t tests):

| Comparison | Naturalness | Persona adherence | Intent adherence |
|---|---:|---:|---:|
| SimTeacher − SFT SimTeacher | −1.3 (p = 0.35) | +6.7 (p = 0.005) | +3.5 (p = 0.10) |
| SimTeacher − SEAD | +16.0 (p < 0.001) | +17.0 (p < 0.001) | −8.0 (p = 0.010) |

## Conclusion

- The lead in the main results is statistically significant: SimTeacher leads the strongest baseline SEAD by 13.7 points (p = 0.002), with confidence intervals not overlapping any baseline.
- The human ratings are reliable: the evaluation is blind, randomized, and independent of the production environment, with inter-rater α of 0.86 / 0.81 among independent raters.
- Simulator quality is stable: relative to SFT SimTeacher with the same initialization, SimTeacher improves persona adherence (p = 0.005); intent adherence rises by 3.5 points (p = 0.10) and naturalness drops by 1.3 points (p = 0.35), both within noise — suggesting that tracking the learnable frontier does not harm simulator quality, consistent with Section 4.3 of the paper.
