# VNEdgeTV_Scripts

TradingView Pine Script (v6) indicators used for discretionary chart analysis
alongside the vnedge trading system. Chart-side tools only — nothing here
feeds vnedge execution.

## Contents

| File | Source | What it is |
|------|--------|------------|
| `vn_edge_trinity_flow.pine` | VN Edge (derivative, MPL-2.0) | **The merged, improved indicator** — Trinity ADX trend engine + NW flow/liquidity engine with all review fixes applied and a combined VN EDGE bias |
| `trinity_adx_pro_bias.pine` | © EMA34TRAD, MPL-2.0 | Original: ADX/DI oscillator with slope-bias table and trend background |
| `nadaraya_watson_liquidity_sweeps.pine` | © AlgoAlpha, MPL-2.0 | Original: causal Nadaraya-Watson slope oscillator with liquidity-sweep levels |
| `CODE_REVIEW.md` | — | Code review of the two originals: correctness findings and design notes |

## VN Edge — Trinity Flow (the merged indicator)

One pane, three layers, one verdict:

- **Trend engine** (from Trinity): ADX/DI via seeded Wilder smoothing
  (`ta.dmi`) — fixes the zero-start bias and the 0/0 → `na` DX of the
  original. Trend labels tier on the *strong* level first, so "Strong
  Uptrend/Downtrend" now actually means ADX > 20 (the original branch was
  unreachable and printed "Strong" from ADX 10).
- **Flow engine** (from AlgoAlpha): causal (non-repainting) Nadaraya-Watson
  slope oscillator, guarded against zero stdev, with signal line, band
  candles, momentum dots and rebound markers (rebounds now test a real curve
  cross: `close[1]` vs `nw_val[1]`).
- **Liquidity engine** (improved): *multiple* levels tracked simultaneously
  via arrays (the original monitored only the newest line), and sweep
  semantics split into **WICK SWEEP** (liquidity grab — dashed line, its own
  alert) vs **CLOSE BREAK** (breakout — dotted line, retired, its own alert).
- **VN EDGE bias**: DI direction + flow sign + flow-vs-signal confluence
  scored −3…+3, graded by ADX tier → STRONG LONG / LONG / LEAN / NEUTRAL /
  SHORT / STRONG SHORT, shown in the table, painted as background, and
  exposed as on/off alerts.

## Key review findings (details in CODE_REVIEW.md)

- **Trinity**: the table's "Strong Uptrend/Downtrend" label fires from the
  *trigger* level (10), not the strong level (20) — the real "Strong Trend"
  branch is unreachable. Also: DX division-by-zero unguarded, Wilder
  accumulators unseeded, slope rows off by one bar.
- **Nadaraya-Watson**: causal kernel — does NOT repaint (good). But the
  oscillator divides by an unguarded rolling stdev, "sweeps" are detected on
  close rather than wick, and only the most recent liquidity level is
  actually monitored — older drawn lines never fire sweep alerts.

Both scripts are unmodified originals; fixes are suggested in the review but
not applied.

## License

Both indicators are subject to the Mozilla Public License 2.0
(https://mozilla.org/MPL/2.0/) per their headers.
