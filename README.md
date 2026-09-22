# Index Integrity — Deflation, Substitution Bias & Goodhart

## Objective

This project audits the integrity of a real-to-nominal deflation pipeline and the metrics built on top of it, correcting a base-year mislabeling error, quantifying CPI-U's upper-level substitution bias against the chained C-CPI-U, and demonstrating how an engagement KPI can invert under Goodhart's Law once it becomes a target.

## Methodology

- Audited an existing deflation pipeline (`deflate_series()`) and identified four defects, including a base-year mislabeling in which output expressed in 1982–84 dollars was reported as 2020 dollars — a class of error that silently corrupts every downstream real-value comparison.
- Rewrote and unit-tested the corrected function, shipped as `deflation_utils.py`, to guarantee base-period consistency between input and output.
- Computed compound annual growth rates (CAGR) for CPI-U and C-CPI-U over a common window, and derived the upper-level substitution bias as the geometric difference between the two rates — the standard, citable form of this estimate — rather than an arithmetic difference of raw index levels.
- Distinguished the annualized index-point drift (a change in level, not a rate) from the percentage-point-per-year substitution bias (a change in growth rate), and explained why the two are not interchangeable despite both being commonly labeled "the gap."
- Computed a rolling-window correlation between DAU/MAU and average time per session to detect a Goodhart's Law inversion — the point at which optimizing a proxy metric causes it to decouple from, or move opposite to, the underlying outcome it was meant to represent.
- Built an interactive Colab monitor (ipywidgets + Plotly) exposing a start-date picker, a CPIAUCNS/CPIAUCSL toggle, and a rolling-window slider, with an automated alert identifying the first window in which the DAU/MAU–session-time correlation turns negative.

## Key Findings

- **Deflation pipeline:** All four defects were confirmed and corrected, most notably a base-year mislabeling that reported 1982–84-dollar output as 2020-dollar output — an error that would understate real values by roughly the cumulative inflation between the two base periods if left uncorrected.
- **Substitution bias:** Over the sample window, CPI-U grew at **2.61%/year** versus **2.35%/year** for C-CPI-U, an upper-level substitution bias of **0.27 pp/year**. Over the same window, the two indexes' raw levels drifted apart by **0.50 index points/year** — a level-drift figure that is *not* directly comparable to the 0.27 pp/year rate, since index points are scale-dependent (relative to an arbitrary base of 100) while the pp/year figure is a compounded, scale-invariant growth-rate difference.
- **Goodhart's Law:** The rolling correlation between DAU/MAU and time per session flipped sign — from **[YOUR VALUE]** to **[YOUR VALUE]** — at the window ending **[YOUR VALUE]**, evidence that once DAU/MAU became a targeted proxy, gains in session frequency began coming at the direct expense of session depth.
