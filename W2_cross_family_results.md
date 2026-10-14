# Full Results Across Agent Scales and Families

This file accompanies the "different agent scales" part of rebuttal W2, giving full training results for SEAD / SimTeacher with two additional agent model families on τ²-bench Retail and Airline. Results for Qwen3-14B are in `W2_full_results.md`.

## Experimental Setup

Same as W2_full_results.md: for each agent family, initialize from the same checkpoint of that family and train in a unified RL framework; re-measure BMG with that family's initial agent (8 trajectories per scenario), fully retrain the simulator, and evaluate the trained agent with a GPT-4o user, 10 trials per task, 3 training seeds. The table reports mean ± sample standard deviation (%); leads are in percentage points. pass^4 uses the τ-bench combinatorial unbiased estimator: for a task with c successes out of 10 trials, take \(\binom{c}{4}/\binom{10}{4}\), then average over tasks.

## Full Results

| Agent | Simulator | Retail pass^1 | Retail pass^4 | Airline pass^1 | Airline pass^4 |
|---|---|---:|---:|---:|---:|
| Gemma-3-12B-it | Initial | 18.2±1.1 | 7.2±1.1 | 10.5±1.0 | 3.1±0.8 |
| Gemma-3-12B-it | SEAD | 39.0±1.4 | 18.9±1.3 | 24.7±1.5 | 9.8±1.2 |
| Gemma-3-12B-it | SimTeacher | **41.2±1.3** | **20.8±1.3** | **28.0±1.3** | **12.3±1.2** |
| Llama-3.1-8B-Instruct | Initial | 6.2±0.8 | 1.6±0.7 | 3.3±0.6 | 0.8±0.8 |
| Llama-3.1-8B-Instruct | SEAD | 25.3±1.3 | 10.3±1.2 | 15.2±1.2 | 5.4±0.9 |
| Llama-3.1-8B-Instruct | SimTeacher | **27.9±1.4** | **12.4±1.3** | **18.2±1.3** | **7.5±1.0** |

## Notes

- Across all four metrics for both families in the table, SimTeacher's mean is higher than SEAD's.
- Airline pass^1 leads: Gemma +3.3, Llama +3.0, in the same direction as Retail.
- Relative to SEAD's own pass^4 level, the relative magnitude of the pass^4 lead exceeds that of pass^1 (e.g., Gemma Airline: +2.5 on a base of 9.8, about +26% relative, vs. pass^1 +3.3 on a base of 24.7, about +13% relative).
- Absolute numbers are uniformly lower than Qwen3-14B, consistent with the ordering of initial capability (initial Retail pass^1: 6.2 / 18.2 / 48.1).

## Conclusion

The pattern in the table is that across agents of different initial capability, SimTeacher shows a positive performance difference over SEAD; the cross-family comparison covers both average success rate and reliability under repeated interactions on Retail and Airline. Since family and scale vary together, this set of experiments does not separately disentangle the two factors.
