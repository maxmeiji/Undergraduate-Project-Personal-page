# Performance evidence audit

## Scope

Reviewed all five PDFs and `0913.pptx` linked on the lab profile. The PDFs are presentation decks about research papers, sometimes including local implementation sections, rather than the original papers themselves. Page numbers below refer to PDF pages; slide numbers refer to the PPTX.

## Hybrid reinforcement learning implementation

Source: [AC + RL presentation](https://nycufinancelab.github.io/LabWebsite/Students/IMF112%E7%BE%85%E6%98%8E%E5%BF%97/A%20reinforcement%20learning%20extension%20to%20the%20Almgren-Chriss%20framework%20for%20optimal%20trade%20execution.pdf), pages 27-36.

- Instrument: Japan Tobacco, 2914.T.
- Training: July 1, 2021 through June 23, 2022; testing: June 24-30, 2022, described as approximately five trading days.
- Sell program: 100,000 shares over six hourly intervals; risk aversion 2e-7.
- Action: multiplier on AC trade size, from 0 to 2 in steps of 0.25.
- Initial discretization: ten inventory/spread/volume states; a later test reduces these to five.
- The presentation says market-depth data were unavailable and assumes a uniform LOB distribution.

| Source page | Configuration / displayed block | RL mean IS | AC mean IS | RL minus AC |
| --- | --- | ---: | ---: | ---: |
| 33 | Ten-state configuration, first block | -0.00325 | -0.00089 | -0.00236 |
| 33 | Ten-state configuration, second block | -0.00083 | 0.00064 | -0.00147 |
| 34 | Five-state configuration, first block | -0.00051 | -0.00089 | +0.00038 |
| 34 | Five-state configuration, second block | -0.00029 | 0.00064 | -0.00093 |

The extracted slide text does not establish what differentiates the two displayed blocks; they should not be assigned train/test labels without further evidence. Units and normalization are not clear enough to convert these values to basis points or currency.

Page 30 uses `VWAP - initial mid price` for the sell-side implementation, a performance convention in which a larger value is better. Under that convention, three displayed comparisons are worse than AC and one is better. The positive difference of 0.00038 must not be advertised as a general RL gain. Ratios against a negative or near-zero baseline would be misleading, and there is no defensible 15% result here.

Page 35 identifies sparse Q tables, possible Q-update issues, and inconsistent inventory/action units. Page 36 proposes shorter time intervals, temporary impact, and DQN as future work. There are no accompanying source code, raw outputs, confidence intervals, or seed-by-seed results in this source set.

## Other project numerical examples

### Almgren-Chriss

Source: [AC presentation](https://nycufinancelab.github.io/LabWebsite/Students/IMF112%E7%BE%85%E6%98%8E%E5%BF%97/Optimal%20Execution%20of%20Portfolio%20Transactions.pdf), page 24. Daily risk-averse realized trading cost is 41,759,718.42 versus naive cost 45,870,027.80, a calculated reduction of **8.96%**. Its expected trading cost is higher than the naive case, which is consistent with trading expected cost against risk. This is a specific non-RL implementation example.

Pages 29 and 33 also display hourly/minute comparisons. The minute table's displayed risk-averse daily allocations do not sum to its stated one-million-share total, so these rows need reconciliation before any volume-normalized or headline claim. The documentation is not a substitute for re-running an equal-volume backtest.

### Bertsimas-Lo

Source: [execution-cost control presentation](https://nycufinancelab.github.io/LabWebsite/Students/IMF112%E7%BE%85%E6%98%8E%E5%BF%97/Optimal%20control%20of%20execution%20costs.pdf), page 16. Actual acquisition cost is 241,463,185.1412 without information and 239,338,913.8219 with information: a calculated reduction of about **0.88%** of gross baseline cost. This uses a different denominator from implementation-shortfall reduction.

Page 28 displays negative acquisition costs and improvement entries that do not reconcile directly with the displayed cost columns. Pages 32 and 67 discuss calibration and information-variable problems. Pages 63 and 66 show realized outcomes worse than naive despite small expected-cost gains. The conclusion's 25%-40% statement is a literature summary, not a verified personal RL achievement.

### General one-sided limit-order book

Source: [LOB presentation](https://nycufinancelab.github.io/LabWebsite/Students/IMF112%E7%BE%85%E6%98%8E%E5%BF%97/OPTIMAL%20EXECUTION%20IN%20A%20GENERAL%20ONE-SIDED%20LIMIT-ORDER%20BOOK.pdf), page 55. Modeled impact cost falls from 5,497,500 to 5,330,667, about **3.03%**, excluding fixed costs. The comparison schedule on that page lists three 1,000-share-equivalent components but also gives terminal residual impact 3,000; the page does not fully specify a consistent equal-total-volume schedule. Treat the percentage as arithmetic on displayed costs, pending schedule verification, rather than a validated benchmark improvement. This is an illustrative analytical model, not reinforcement learning.

## Prior-literature results

| Source | Result in the presentation | Attribution |
| --- | --- | --- |
| [Hybrid AC + RL](https://nycufinancelab.github.io/LabWebsite/Students/IMF112%E7%BE%85%E6%98%8E%E5%BF%97/A%20reinforcement%20learning%20extension%20to%20the%20Almgren-Chriss%20framework%20for%20optimal%20trade%20execution.pdf), p.23 | 4.8% IS improvement, South African equities | Hendricks and Wilcox |
| [Optimized execution RL](https://nycufinancelab.github.io/LabWebsite/Students/IMF112%E7%BE%85%E6%98%8E%E5%BF%97/Reinforcement%20Learning%20for%20Optimized%20Trade%20Execution.pdf), p.15 | 27.16%-35.50% over submit-and-leave with private state | Nevmyvaka, Feng and Kearns |
| [Optimized execution RL](https://nycufinancelab.github.io/LabWebsite/Students/IMF112%E7%BE%85%E6%98%8E%E5%BF%97/Reinforcement%20Learning%20for%20Optimized%20Trade%20Execution.pdf), pp.2,21 | Improvements up to 50% or more | Same earlier research; not the cooperation project's test |

These values cannot be transferred to a CV as personal implementation results.

## PPO metrics

Source: [PPO deck](https://nycufinancelab.github.io/LabWebsite/Students/IMF112%E7%BE%85%E6%98%8E%E5%BF%97/0913.pptx), slides 23-27, including embedded result tables inspected visually.

The title slide attributes the research to Siyu Lin and Peter A. Beling, IJCAI 2020, and dates the presentation September 15, 2023. The experiment uses 14 US equities, a nine-month/three-month training/test split, and one-minute execution episodes. This differs from the project's Japanese-equity hybrid implementation.

- Slide 24 reports mean `delta IS = IS_model - IS_TWAP`. For BA, PPO-LSTM is **72.53** and PPO-Stack is **13.35**; these are differences, not percentages.
- Slide 25 reports the standard deviation of IS. For BA, PPO-LSTM is **294.09**, compared with TWAP **335.05**. This is approximately **12.22% lower IS standard deviation**, calculated from this literature table. It measures dispersion, not execution-cost improvement or a personal result.
- Slide 26 reports gain-loss ratios. BA PPO-LSTM is **1.54** and VWAP **0.59**. Ratios are neither percent gains nor realized returns.
- PPO does not win for every stock: slide 24 shows negative mean differences for PPO-LSTM on GS and MCD and PPO-Stack on MCD and NVDA.

The extracted mathematical text around the shaped reward is fragmented and its sign convention is not safe to reproduce as implementation code. A future implementation should verify it against the original paper.

## How to establish a defensible improvement claim

Use a consistent cost convention, with positive values representing worse execution:

- Buy cost: `sum(q_i * (p_i - p_0)) + fees`.
- Sell cost: `sum(q_i * (p_0 - p_i)) + fees`.
- Normalized cost in basis points: `10,000 * cost / (Q * p_0)`.
- Paired improvement in bps: `mean(baseline_cost_bps - RL_cost_bps)`.
- Relative reduction: `100 * (mean baseline cost - mean RL cost) / mean baseline cost`, only with a positive, meaningful baseline denominator.

Use the same instruments, order sides/sizes, time windows, fill constraints, market impact, fees, and terminal liquidation rules for each strategy. Report test sample size, held-out dates, mean/median bps, dispersion, confidence intervals, completion rate, and multiple training seeds. An improvement in reward, gross purchase cost, or IS dispersion is not interchangeable with a reduction in slippage.

**Conclusion:** the public materials support a substantive execution-research portfolio, but not the supplied claim of over 15% RL execution-efficiency improvement. Raw backtest results or a later thesis report could provide additional evidence.
