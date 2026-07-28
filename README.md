# Factor Forensics

**Do published stock-market anomalies keep working — and can a factor built from free data even measure them honestly?**

This project investigates the reliability of published equity-return "anomalies" from two angles. First, whether their documented predictability survives *out-of-sample*, after the data the original researchers studied. Second, whether a momentum factor rebuilt from scratch on free data can reproduce the professional benchmark — and what the gap between them reveals. The recurring theme is skepticism: distrusting a clean-looking result until it survives contact with data nobody had seen when the finding was made.

---

## Part 1 — Do anomalies survive out-of-sample?

*Notebook: `anomly_decay.ipynb`*

Using the [Open Source Asset Pricing](https://www.openassetpricing.com/) (OSAP) database of Chen & Zimmermann, I took 138 high-quality published anomalies, split each at the end of *its own original study's sample period*, and compared performance in-sample vs. out-of-sample. The split is drawn at each signal's `SampleEndYear` — not its publication year — because the returns after the original data ended are the first ones nobody had seen when the anomaly was formed, and therefore the honest test.

**The finding:**

- **106 of 138 anomalies (77%) decayed** out-of-sample, with the median anomaly losing about **2.8 percentage points** of annualized return.
- Of the **70 anomalies statistically significant in-sample** (Newey-West *t* > 2), only **16 remained significant out-of-sample**, and just **5** cleared a strict *t* > 3 bar.

![In-sample vs out-of-sample significance](chart1_tstat_scatter.png)

*Each point is one anomaly, plotted by its Newey-West t-statistic during vs. after its original study. Points below the diagonal weakened out-of-sample — most did. The survivor quadrant (upper-right, significant on both axes) is sparse.*

This replicates, on independently processed data, the central result of McLean & Pontiff (2016): published predictability tends to erode once a pattern is public, consistent with arbitrage capital flowing toward it. The practical takeaway — an impressive in-sample t-stat is a weak promise about the future; a published edge is best treated as an upper bound on what remains.

---

## Part 2 — Building momentum from scratch

*Notebook: `momentum.ipynb`*

A finding is only as trustworthy as the data behind it. So I rebuilt the classic 12-month momentum long-short from *raw* Yahoo Finance prices — no pre-computed portfolios — and validated it against OSAP's professional CRSP-based benchmark.

Building from free data surfaced exactly the messiness that professional databases pre-clean: an impossible -115% portfolio month traced to a single bad price (fixed by winsorizing 46 of 174,086 stock-month returns), and a subtle one-month date-alignment bug that silently drove the benchmark correlation to near-zero until diagnosed.

**The finding:**

- My homemade factor correlates **0.79** with the professional benchmark — it captures the same effect.
- But it earns only about a third of the value-weight benchmark's return (~5% vs ~15% annualized). **Most of that gap is weighting, not survivorship:** against a *matched equal-weight* benchmark the shortfall roughly halves (~10.3pp to ~4.3pp). The residual reflects my large-cap-only universe (momentum is strongest in smaller stocks) and survivorship (today's index excludes the delisted losers a short-momentum leg profits from).

![Momentum vs benchmark](mom_vs_osap.png)

*Same pattern, different magnitude. The gap decomposes: matching the weighting scheme closes more than half of it — honest attribution requires benchmarking against matched constructions rather than assuming a single cause.*

---

## What this project demonstrates

Two complementary skills applied to the same skeptical question. Part 1 is a rigorous multi-signal empirical study (proper out-of-sample design, autocorrelation-robust statistics, honest conditioning). Part 2 is hands-on data engineering (constructing a factor from raw prices, diagnosing real data glitches, decomposing a performance gap against matched benchmarks). Both center on the same discipline: interrogating results that look too clean.

## Honest limitations

- Part 1 is a **replication**, not novel research — its value is rigor and independent processing, not originality.
- Recently-published anomalies have short out-of-sample windows, making their t-stats noisier.
- OSAP's standardized decile construction differs from each author's exact original methodology, so some measured decay may reflect construction.
- The momentum universe is survivor-only and large-cap-only; the homemade-vs-benchmark gap conflates weighting, universe, and survivorship, which free data cannot fully separate.

## Reproducing

Both notebooks run top to bottom on a fresh kernel and pull data live (OSAP package + Yahoo Finance), so an internet connection is required.

**Tools:** Python, pandas, statsmodels (Newey-West / HAC), matplotlib, yfinance, openassetpricing.

---

*Self-directed research project. Data: Open Source Asset Pricing (Chen & Zimmermann).*
