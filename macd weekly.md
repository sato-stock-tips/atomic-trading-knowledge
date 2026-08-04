The 6/20 MACD on a weekly chart does not confirm uptrends better than the 12/26 version; it does the opposite by construction. Faster parameters lower the bar for what registers as momentum, producing earlier but less durable signals. This directly conflicts with a goal of entering only confirmed uptrends on liquid leaders, where the priority is sustained institutional-grade momentum rather than the first hint of a move.

Step-by-step breakdown of the indicator itself

MACD measures the relationship between two exponential moving averages (EMAs) of price:

[ \text{MACD Line} = \text{EMA}{\text{fast}}(P_t) - \text{EMA}{\text{slow}}(P_t) ]

[ \text{Signal Line} = \text{EMA}_9(\text{MACD Line}) ]

[ \text{Histogram} = \text{MACD Line} - \text{Signal Line} ]

Standard settings are MACD(12, 26, 9). The faster variant referenced as “6/20” is typically MACD(6, 20, 9) or MACD(6, 20, 10); the signal period difference is secondary. The core distinction lives in the fast and slow EMAs.

The EMA smoothing constant for any period (n) is:

[ \alpha = \frac{2}{n + 1} ]

- Period 6: (\alpha \approx 0.286) (28.6 % weight on the newest weekly bar)
- Period 12: (\alpha \approx 0.154) (15.4 % weight)
- Period 20: (\alpha \approx 0.095)
- Period 26: (\alpha \approx 0.074)

Higher (\alpha) makes the 6/20 version far more responsive to the most recent weekly closes. The MACD line and histogram therefore oscillate with greater amplitude and frequency.

Translation to weekly timeframe (real calendar time)

Each bar on the weekly chart aggregates one week of trading.

- 12/26 weekly: Fast EMA looks back roughly 12 weeks (~3 months). Slow EMA looks back ~26 weeks (~6 months). Signal ~9 weeks (~2 months). This matches the original design intent of the indicator when markets operated on 6-day weeks (12 periods ≈ 2 weeks, 26 periods ≈ 1 month). On today’s 5-day weeks it remains an intermediate-to-longer-term tool.
- 6/20 weekly: Fast EMA ~6 weeks (~1.5 months). Slow EMA ~20 weeks (~4.5–5 months). The entire construction behaves like a transposed daily MACD dropped onto weekly bars. It reacts to moves that the 12/26 largely ignores.

The 6/20 therefore detects momentum shifts on a 1–5 month horizon; the 12/26 requires persistence across a 3–6+ month horizon.

Signal behavior differences

Faster settings increase the raw number of crossovers and zero-line flips. Sources that directly compared the two (e.g., side-by-side tests on the same liquid names) show the 6,20,10 variant generates materially more signals than 12,26,9. The extra signals come from both legitimate early turns and from noise that the slower pair filters out.

On a weekly chart this effect is amplified because the underlying bars are already smoothed. Adding an ultra-responsive 6/20 layer re-introduces short-term chop that the weekly aggregation was meant to remove. In ranging or mildly trending conditions common even among liquid leaders, the 6/20 will print bullish crossovers that reverse within a few bars. The 12/26 stays flat or negative until the move has demonstrated durability.

Histogram behavior follows the same pattern: 6/20 histogram bars expand and contract more violently; 12/26 histogram turns are slower to develop but more reliable as measures of sustained force.

Application to the stated goal — entering confirmed uptrends on liquid leaders

“Confirmed uptrend” implies momentum that has already overcome resistance and is likely to persist, not the first crossover after a basing period. Liquid leaders (high-volume names with institutional sponsorship, clean order flow, and typically strong underlying fundamentals) produce the cleanest sustained trends precisely because large participants accumulate or distribute over weeks to months. A slower indicator aligns with that time scale.

Using 12/26 weekly for confirmation:

- Bullish MACD > signal + MACD > 0 sustained for multiple bars = higher-conviction evidence that the fast EMA has pulled decisively above the slow EMA across a multi-month window.
- Fewer but higher-quality entry points. This reduces whipsaw exposure and allows larger position sizing with defined risk (e.g., stop below a prior weekly pivot or the signal line itself).
- Better compatibility with weekly price structure (higher highs/lows, breakout from consolidation) because both the indicator and the chart operate on comparable horizons.

Using 6/20 weekly for the same purpose:

- Generates earlier alerts, which can feel advantageous for “getting in” but violates the confirmation requirement. Many of those early crosses fail or produce only brief rallies before the slower 12/26 structure reasserts control.
- Increases trade frequency on the weekly frame, which for a position-oriented approach on leaders is usually counterproductive (more commissions, slippage on entries/exits, tax events in jurisdictions that matter to cross-border traders, and psychological overtrading).
- In strong trending legs the 6/20 will oscillate around the signal line even while price makes higher highs, tempting premature exits or unnecessary re-entries.

The mismatch is structural: the 6/20 was popularized for short-term intraday work (notably on 5-minute charts). Transplanting it to weekly without adjustment turns a confirmation tool into a short-term momentum oscillator. That is useful for timing within an already confirmed trend, not for establishing the trend itself.

Practical implementation notes for weekly liquid-leader screening

1. Primary filter: 12/26/9 weekly. Require MACD line above signal and above zero, preferably with histogram turning up after a period of contraction. Add price confirmation (close above key weekly moving average or prior swing high).
2. Optional earlier alert layer: Run 6/20/9 on the same weekly chart or drop to daily 6/20 or standard 12/26. Only act on the faster signal when the weekly 12/26 is already aligned bullish. This hybrid captures some earliness without sacrificing the confirmation threshold.
3. Market regime awareness: In strong bull markets both settings work; the 12/26 simply lets winners run longer with less interference. In choppy or distribution phases the 6/20 will generate repeated false positives exactly when confirmation is most needed.
4. Position management: With the slower setting, stops can sit farther away (below recent weekly structure) while still offering positive expectancy because false signals are rarer. Faster settings force tighter stops or more frequent management, raising the chance of being shaken out of valid moves on liquid names that routinely pull back 5–10 % intra-trend.

Summary of the core difference

- 6/20 weekly = higher sensitivity, earlier detection, more signals, lower confirmation quality, higher whipsaw risk. Appropriate as a tactical timing tool once trend is established.
- 12/26 weekly = lower sensitivity, later but more durable signals, fewer trades, higher confirmation quality. Better matched to “enter in confirmed uptrend on liquid leaders.”

The 12/26 version is the one that actually operationalizes the goal as stated. The 6/20 version optimizes for speed at the direct expense of confirmation reliability. On weekly charts of liquid stocks, that trade-off is usually unfavorable for a trend-entry discipline. Test both side-by-side on your specific universe (including Korean names if that is part of the book) over multiple market regimes; the slower pair will show cleaner equity curves when the objective is high-conviction participation in established moves rather than maximum signal count.