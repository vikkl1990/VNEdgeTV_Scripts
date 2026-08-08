# VNEdgeTV_Scripts

TradingView Pine Script (v6) indicators used for discretionary chart analysis
alongside the vnedge trading system. Chart-side tools only — nothing here
feeds vnedge execution.

## Contents

| File | Source | What it is |
|------|--------|------------|
| `trinity_adx_pro_bias.pine` | © EMA34TRAD, MPL-2.0 | ADX/DI oscillator with slope-bias table and trend background |
| `nadaraya_watson_liquidity_sweeps.pine` | © AlgoAlpha, MPL-2.0 | Causal Nadaraya-Watson slope oscillator with liquidity-sweep levels |
| `CODE_REVIEW.md` | — | Code review of both scripts: correctness findings and design notes |

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
