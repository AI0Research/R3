# Training Configuration and Correlations for the BMG Validation Experiments (R1a, R1b)

This file documents the full configuration of experiment R1a (whether BMG measured before training predicts actual improvement after training) and experiment R1b (whether increasing the BMG weight in the objective yields larger improvement), corresponding to the W1 part of our reply.

## Shared Configuration (common to R1a and R1b)

All settings not listed are identical to the paper (Appendix, Training Configuration).

| Item | Configuration |
|---|---|
| Agent initial checkpoint | Qwen3-14B |
| Scenario pool | τ²-bench Airline: 30 training tasks × compatible persona combinations, 320 scenarios in total |
| BMG measurement | For each scenario, sample 8 trajectories with the initial agent (temperature 0.7); BMG = max − mean of the 0/1 task reward over the 8 trajectories |
| Training algorithm | Full-parameter GRPO |
| GRPO learning rate | 1e-6 |
| KL coefficient | 0.001 |
| Agent group sampling | 4 trajectories per task, temperature 0.7 |
| Agent RL effective batch size | 8 |
| Training budget | Fixed 60 optimization steps per subset / weight setting (no early stopping) |
| Max interaction turns | 50 |
| Agent decoding | nucleus p=0.95, top-k=20, max generation length 2,048 per call |
| Seeds | 3 random training seeds per setting (42 / 1234 / 2026); subsets are fixed across seeds, only training randomness varies |
| Evaluation | 20 held-out Airline test tasks (disjoint from the 30 training tasks), GPT-4o user, 10 trials per task (pass^1 denominator 200, granularity 0.5 percentage points), same as the main table R2 |

## R1a: Subset Composition

We compare three training subsets of 40 scenarios each with different average BMG, using three random subsets of the same size as controls. Each subset contains scenarios from different BMG ranges; the exact composition is given in the table. "Low-BMG", "Mid-BMG", and "High-BMG" are named by the subset's average BMG, not by requiring every scenario in the subset to fall in the corresponding range.

| Subset | Scenario counts (low / mid / high / zero bucket) | Size | Average BMG |
|---|---|---:|---:|
| Low-BMG | 12 / 8 / 4 / 16 | 40 | 0.27 |
| Mid-BMG | 4 / 18 / 8 / 10 | 40 | 0.43 |
| High-BMG | 2 / 6 / 22 / 10 | 40 | 0.55 |
| Random 1 | 0 / 4 / 8 / 28 | 40 | 0.21 |
| Random 2 | 1 / 8 / 11 / 20 | 40 | 0.34 |
| Random 3 | 0 / 2 / 16 / 22 | 40 | 0.35 |

Composition is bucketed by per-scenario BMG: the low bucket is (0, 0.3], the mid bucket is (0.3, 0.7), the high bucket is [0.7, 0.875], and the zero bucket is BMG = 0 (all 8 trajectories fail or all succeed).

Notes:
- The Low-BMG, Mid-BMG, and High-BMG subsets all retain BMG = 0 scenarios, at 40%, 25%, and 25% respectively.
- Training subsets are fixed across the three training seeds; all agents are initialized from the same checkpoint and trained for 60 steps in the same RL framework, with only the scenario composition differing; the scenario order within each subset is reshuffled per epoch according to the seed.

### R1a Per-Seed Results

The table reports per-seed Airline pass^1 results, converted to percentages from the number of successes among the 200 evaluation dialogues per checkpoint; subset compositions and average BMG are in the table above.

| Subset | Average BMG | seed 42 | seed 1234 | seed 2026 | Mean ± SD | Improvement over initial agent |
|---|---:|---:|---:|---:|---:|---:|
| — (initial agent, 3 evaluations) | — | 22.50 | 23.50 | 25.50 | 23.8 ± 1.5 | — |
| Random 1 | 0.209 | 22.50 | 26.00 | 23.50 | 24.0 ± 1.8 | +0.2 |
| Random 2 | 0.341 | 28.50 | 26.50 | 27.00 | 27.3 ± 1.0 | +3.5 |
| Random 3 | 0.347 | 27.00 | 25.50 | 29.00 | 27.2 ± 1.8 | +3.4 |
| Low-BMG | 0.266 | 25.00 | 23.00 | 27.50 | 25.2 ± 2.3 | +1.4 |
| Mid-BMG | 0.434 | 28.50 | 26.00 | 30.50 | 28.3 ± 2.3 | +4.5 |
| High-BMG | 0.553 | 31.00 | 28.50 | 28.50 | 29.3 ± 1.4 | +5.5 |

How the correlation coefficients are computed: using the 18 seed-level results (6 subsets × 3 seeds), the correlation between average BMG and improvement is Spearman ρ = 0.73, Pearson r = 0.74. The three seeds of a subset share the same scenario composition, so average BMG takes only 6 independent values; we therefore do not compute a p value with n = 18, but use a subset-level permutation test: keeping each subset's three seeds together and permuting the BMG labels of the 6 subsets over all 720 permutations gives p = 0.003. Spearman is on the lower side because for subsets with similar BMG (Random 2 vs. Random 3, 0.341 vs. 0.347) the improvement difference is smaller than the seed noise, so their ranks can swap; the gap between the High-BMG and Low-BMG subsets (+4.1) is clearly larger than the seed noise.

Choice of evaluation count: we follow the main table R2 with 10 trials per task (200 dialogues per checkpoint); the seed standard deviations (1.0–2.3) are on the same order as the main-table Airline pass^1 (1.3–2.0).

## R1b: Reward-Term Weights

The simulator objective (Eq. 6 in the paper) consists of the BMG term (weight λ_l), the coverage reward (weight λ_c), and a KL constraint. R1b varies only λ_l, following the paper's full alternating procedure of training the simulator first and then the agent; all settings use the same RL framework and the same number of steps:

| λ_l | λ_c (coverage) | KL coefficient | Note |
|---:|---:|---:|---|
| 0 | 1.0 | 0.001 | BMG term removed entirely |
| 0.25 | 1.0 | 0.001 | |
| 0.5 | 1.0 | 0.001 | |
| 1.0 | 1.0 | 0.001 | Paper setting |

R1b per-seed results (evaluation setup identical to R1a, 10 trials per task, successes / 200):

| λ_l | seed 42 | seed 1234 | seed 2026 | Mean ± SD | Improvement over initial agent |
|---:|---:|---:|---:|---:|---:|
| 0 | 25.00 | 27.50 | 28.00 | 26.8 ± 1.6 | +3.0 |
| 0.25 | 25.50 | 27.50 | 28.50 | 27.2 ± 1.5 | +3.4 |
| 0.5 | 27.50 | 29.50 | 31.00 | 29.3 ± 1.8 | +5.5 |
| 1.0 | 30.50 | 33.00 | 33.50 | 32.3 ± 1.6 | +8.5 |

Consistency checks:
- R1a and R1b use the same evaluation setup; the λ_l = 0 result (+3.0) is at the same level as R1a's random subsets (+0.2 / +3.5 / +3.4): with the BMG term removed, the simulator degenerates to coverage-driven scenario selection.
- λ_l = 0.25 is indistinguishable from λ_l = 0 within seed noise (+3.4 vs. +3.0); from λ_l ≥ 0.5 the improvement grows progressively (+5.5, +8.5), while the gap between 0.5 and 1.0 (3.0) is still within seed noise.
- The λ_l = 1.0 result (+8.5) is comparable to R1a's best training subset (High-BMG, +5.5).

## Correlation Statistics

The reviewer asked for the correlation between the gap and actual improvement. Average BMG vs. post-training improvement for the six training subsets (Low-BMG, Mid-BMG, High-BMG, and the three random subsets, sorted by average BMG; per-seed values are in the R1a per-seed table):

| Subset | Average BMG | Improvement |
|---|---:|---:|
| Random 1 | 0.209 | +0.2 |
| Low-BMG | 0.266 | +1.4 |
| Random 2 | 0.341 | +3.5 |
| Random 3 | 0.347 | +3.4 |
| Mid-BMG | 0.434 | +4.5 |
| High-BMG | 0.553 | +5.5 |

Computed on the 18 seed-level results: Spearman ρ = 0.73, Pearson r = 0.74; subset-level permutation test (720 permutations) p = 0.003. The only non-monotone pair is Random 2 vs. Random 3: their average BMG differs by only 0.006 and their improvement by 0.1 points, within seed noise.

## Conclusion

R1a shows that the subset-average BMG measured **before** training correlates positively with actual improvement after training (18 seed-level results, Spearman ρ = 0.73, Pearson r = 0.74; subset-level permutation test p = 0.003). When BMG gaps are large the improvement gaps are clear (the High-BMG subset exceeds the Low-BMG subset by 4.1 percentage points), while subsets with similar BMG stay within seed noise. R1b shows that this relationship can be exploited: with the BMG term's weight at 0.5 or above, improvement is 2.5–5.5 percentage points higher than with the term removed; with a very small weight the effect is indistinguishable from removing it. Both sets of results constitute correlational evidence plus one weight-only intervention, not a strict causal estimate.
