# 7-11 Rule Maximum Entry ATR% Multiple

_What is the maximum ATR% multiple from the 50 SMA at which you can buy and still have positive expected equity value, given the 7-11 exit rule?_

---

## Result

With a **70% failure rate**, an **initial stop at 0.5× ATR% below entry**, and **ATR% expanding 20% from entry to 11×**, the maximum entry for positive expected equity value is approximately **4.0× ATR% from the 50 SMA**:

| Assumptions                                               | Maximum entry X |
| --------------------------------------------------------- | --------------- |
| Stop distance s = 0.25, SIW at 50% of peak                | **4.8× ATR%**   |
| Stop distance s = 0.50, SIW at 40% of peak (conservative) | **3.5× ATR%**   |
| **Stop distance s = 0.50, SIW at 50% of peak (baseline)** | **4.0× ATR%**   |
| Stop distance s = 0.50, SIW at 60% of peak                | **4.4× ATR%**   |
| Stop distance s = 1.00, SIW at 50% of peak                | **2.2× ATR%**   |

The dominant drivers are the **initial stop distance** (how tight your stop is) and the **failure rate**. The SIW assumption barely moves the result because checkpoint gains dominate the tail.

---

## Key Concept: The 1/3 Trim Always Captures 1× ATR%

The 1/3 sold 3–5 days after entry is sold at **1× ATR% above the entry point**. If you enter at X × ATR%₀ from the 50 SMA, the 1/3 is sold at (X + 1) × ATR%₀ from the 50 SMA. This means the gain on the 1/3 is always **exactly 1× ATR%₀ per share**, regardless of where you enter:

- Enter at 0× (at the SMA) → 1/3 sold at 1× → gain = 1
- Enter at 2× → 1/3 sold at 3× → gain = 1
- Enter at 4× → 1/3 sold at 5× → gain = 1

This is a fixed "rent" you collect for holding the position 3–5 days. It doesn't scale with X, which means at high entry multiples the 1/3 contributes less proportionally — but it always contributes exactly 1 per share.

---

## Setup

### Entry and Stops

- **Single entry** at **X** × ATR%₀ above the 50 SMA
- **1/3 sold at (X + 1) × ATR%₀** from the 50 SMA (3–5 days after entry). Gain is always exactly 1 × ATR%₀ per share.
- **Initial stop loss** placed at entry, **s** × ATR%₀ below entry price (s = 0.25 to 1.0 per [[stop loss]])
- **20 EMA manual stop** — closes remaining position when price closes below the 20 EMA (per [[sell into weakness]])
- **70% failure rate** — initial stop is hit before reaching 7×
- **30% success rate** — stock reaches 7× and the 7-11 rule activates

### ATR% Expansion

ATR% increases 20% from the starting ATR%₀ to the ending ATR% at 11×. Linear interpolation across checkpoints:

| Checkpoint K | ATR%(K) / ATR%₀ | Price extension from SMA |
|-------------|-------------------|--------------------------|
| X+1 (1/3 sold here) | 1.00 | (X + 1) × ATR%₀ |
| 7× | 1.00 | 7.00 × ATR%₀ |
| 8× | 1.05 | 8.40 × ATR%₀ |
| 9× | 1.10 | 9.90 × ATR%₀ |
| 10× | 1.15 | 11.50 × ATR%₀ |
| 11× | 1.20 | 13.20 × ATR%₀ |

No expansion is assumed below 7× — ATR% = ATR%₀ at the 1/3 checkpoint and at 7×.

### Exit Fractions (per [[69 take profit rule]])

| Fraction | Sold at | Shares per 1,000 | Gain multiple (above entry) |
|----------|---------|-------------------|-----------------------------|
| 1/3 | (X + 1) × ATR%₀ from SMA | 333 | **1** (always, regardless of X) |
| 1/5 | 7.00 × ATR%₀ from SMA | 200 | (7.00 − X) |
| 1/8 | 8.40 × ATR%₀ from SMA | 125 | (8.40 − X) |
| 1/13 | 9.90 × ATR%₀ from SMA | 77 | (9.90 − X) |
| 1/21 | 11.50 × ATR%₀ from SMA | 48 | (11.50 − X) |
| 1/34 | 13.20 × ATR%₀ from SMA | 29 | (13.20 − X) |
| 188/1000 | Sell into weakness (20 EMA) | 188 | Varies by scenario |

### 20 EMA Widening (Sell-into-Weakness)

The 20 EMA rises above the 50 SMA as the stock advances. SIW shares are sold when price closes below the 20 EMA. Estimated 20 EMA level as a fraction of the peak extension from the 50 SMA:

| Peak scenario | Peak extension | SIW at 40% | SIW at 50% | SIW at 60% |
|--------------|---------------|-----------|-----------|-----------|
| Peak 7× | 7.0 × ATR%₀ | 2.8 | 3.5 | 4.2 |
| Peak 8× | 8.4 × ATR%₀ | 3.4 | 4.2 | 5.0 |
| Peak 9× | 9.9 × ATR%₀ | 4.0 | 5.0 | 5.9 |
| Peak 10× | 11.5 × ATR%₀ | 4.6 | 5.75 | 6.9 |
| Peak 11× | 13.2 × ATR%₀ | 5.3 | 6.6 | 7.9 |

---

## Continuation Probabilities

From [[denis__hamel]](https://x.com/denis__hamel)'s 10-year study of 3,806 CLEAN legs reaching ≥ 7 ATR above SMA50:

| Transition | P(K+1 \| at K) |
|------------|----------------|
| 7→8 | ~35% |
| 8→9 | ~45% |
| 9→10 | ~35% |
| 10→11 | ~25% |

Derived unconditional probabilities (given entry):

| Peak at | P(peak = K \| at 7×) | P(peak = K \| entry) |
|---------|----------------------|---------------------|
| Failure (never reaches 7×) | — | **70.000%** |
| 7× | 65.00% | 19.500% |
| 8× | 19.25% | 5.775% |
| 9× | 10.24% | 3.071% |
| 10× | 4.13% | 1.240% |
| 11×+ | 1.38% | 0.413% |

---

## Expected Value Calculation

### On Failure (70%)

All shares hit the initial stop. Loss per share = **s** (in normalized units of ATR%₀ × SMA price), where s is the stop distance (0.25–1.0). This is **independent of X** — whether you entered at 1× or 5×, the loss is always s × ATR%₀ × SMA_price.

### On Success (30% total, split across sub-scenarios)

Scenario payoffs with **baseline assumptions** (s = 0.5, SIW at 50% of peak):

The 1/3 always contributes a gain of **1** per share. The X coefficient for the 1/3 is **0** (its gain doesn't depend on X). The remaining 2/3 of the position has an X coefficient that sums to **−0.667**.

| Scenario | P | 1/3 @ (X+1) | Remaining fractions | Per-share gain |
|----------|---|-------------|---------------------|---------------|
| Failure | 0.700 | — | — | **−0.500** (stop loss) |
| Peak 7× | 0.195 | 1 (fixed) | 1/5+(SIW) at 7× and 3.5 | 3.368 − 0.667X |
| Peak 8× | 0.058 | 1 (fixed) | 1/5+1/8+(SIW) at 7×, 8.4, 4.2 | 4.219 − 0.667X |
| Peak 9× | 0.031 | 1 (fixed) | 1/5+1/8+1/13+(SIW) at 7×, 8.4, 9.9, 5.0 | 4.870 − 0.667X |
| Peak 10× | 0.012 | 1 (fixed) | +1/21+(SIW) at 5.75 | 5.345 − 0.667X |
| Peak 11×+ | 0.004 | 1 (fixed) | +1/34+(SIW) at 6.6 | 5.721 − 0.667X |

### Solving for Maximum X

$$E[\text{gain}] = P(\text{fail}) \times (-s) + (1-P(\text{fail})) \times (\text{weighted avg gain} - 0.667X)$$

With s = 0.50 and SIW at 50%:

$$E = 0.70 \times (-0.50) + 0.30 \times (3.800 - 0.667X)$$

$$= -0.350 + 1.140 - 0.200X = 0.790 - 0.200X$$

Setting E[gain] = 0:

$$X_{\max} = \frac{0.790}{0.200} = 3.95 \approx 4.0$$

---

## Sensitivity Analysis

### Stop distance (s)

The stop distance is the single most important parameter after the failure rate:

| Stop distance s | SIW at 40% | SIW at 50% | SIW at 60% |
|----------------|-----------|-----------|-----------|
| 0.25 (tight) | 4.4 | **4.8** | 5.3 |
| 0.50 (baseline) | 3.5 | **4.0** | 4.4 |
| 1.00 (wide) | 1.7 | **2.2** | 2.7 |

### Failure rate

| Failure rate | X_max (s = 0.5, SIW 50%) |
|-------------|--------------------------|
| 50% | 4.9 |
| 60% | 4.6 |
| **70%** | **4.0** |
| 75% | 3.4 |
| 80% | 2.7 |

### Why X_max ≈ 4 and not lower

The key insight is the **initial stop caps failure losses at s** (typically 0.5), not at X. At X = 4, you enter 4× ATR% above the 50 SMA, but you only lose 0.5× ATR% if stopped out — not 4×. The 1/3 trim always captures 1× ATR% of gain. Together, these mean:

- On failure (70%): you lose s = 0.5 per share
- On success (30%): the 1/3 alone gives you 0.333 per share (fixed)
- The 1/5 at 7× adds (7 − X) × 0.200 per share in success scenarios
- At X = 4, the 1/5 gives 0.6 per share in success scenarios — still positive

The 1/3 "rent" of exactly 1× ATR% per share is what makes entries up to 4× viable despite a 70% failure rate.

### Contribution breakdown (at X = 0, s = 0.5)

| Component | Contribution to E[gain] | How computed |
|-----------|----------------------|-------------|
| 1/3 sold at (X+1) | +0.100 | P(success) × (1/3) × 1.0 = 0.30 × 0.333 × 1.0 |
| 1/5 sold at 7× | +0.420 | P(success) × (1/5) × 7.0 = 0.30 × 0.200 × 7.0 |
| Higher checkpoints (8×–11×) | +0.157 | Weighted by reach probability and fraction |
| SIW tail (20 EMA stop) | +0.463 | Remaining shares sold at trailing 20 EMA levels |
| Failure loss (−s) | −0.350 | P(failure) × s = 0.70 × 0.50 |
| **Total at X = 0** | **+0.790** | X_max = 0.790 / 0.200 = 3.96 ≈ 4.0 |

---

## Assumptions

1. **Single entry point** at X × ATR%₀ above the 50 SMA. No scaling in.

2. **1/3 sold at (X + 1) × ATR%₀** from the 50 SMA, 3–5 days after entry. The gain on the 1/3 is always exactly 1 × ATR%₀ per share, regardless of X. This models selling one ATR% above the entry point — a modest trim that removes risk early.

3. **70% failure rate** — 7 out of 10 trades are stopped out on the initial stop before reaching 7× ATR%. Assumed constant regardless of X. In reality, entries closer to the SMA likely have a lower failure rate.

4. **Two stop losses**: (a) An initial stop placed at entry, s × ATR%₀ below the entry price (s = 0.25 to 1.0 per [[stop loss]]). This stop stays in place and is hit if the stock immediately fails. (b) A manual [[sell into weakness]] stop — close the entire remaining position only if price closes below the 20 EMA. Not a placed order; a rules-based stop.

5. **ATR% expands 20%** from the starting ATR%₀ to ATR% at 11× (i.e., ATR%(11) = 1.20 × ATR%₀). Linear interpolation across checkpoints 7→11. Below 7×, no expansion (ATR% = ATR%₀). The 1/3 at (X+1) uses unexpanded ATR%₀.

6. **20 EMA widens from the 50 SMA** as the stock advances. The SIW trailing level is estimated at 40–60% of the peak ATR% extension from the 50 SMA, depending on how long and how fast the move has been.

7. **Continuation probabilities from Denis Hamel's data** — P(8|7) ≈ 35%, P(9|8) ≈ 45%, P(10|9) ≈ 35%, P(11|10) ≈ 25%. Averages across liquidity tiers. Lightly-traded names have higher continuation.

8. **Unreached checkpoint fractions** are sold at the 20 EMA level (same as the SIW tail), not at the SMA or the initial stop. This reflects that in success scenarios, the 20 EMA is above the SMA and above the initial stop.

9. **No transaction costs, slippage, or taxes** — real-world friction would reduce X_max.

10. **The 1/3 sold at (X+1) is a time-based exit** independent of the 7-11 checkpoints. It models the [[sell into strength]] rule of selling one-third 3–5 days after entry, at approximately one ATR% above the entry point.

11. **ATR%₀ is constant at entry** — the ATR% at the time of entry is the baseline. In reality, ATR% changes over time.

12. **All scenarios are mutually exclusive** — the stock peaks at exactly one level (7, 8, 9, 10, or 11+), then reverses. No partial revisits to earlier checkpoints after reaching a higher one.

13. **Position size is 1,000 shares** — used for the fractional breakdown, but the per-share expected value calculation is independent of position size.

14. **The initial stop distance s is independent of X** — the stop is placed s × ATR%₀ below the entry regardless of where the entry is relative to the 50 SMA. Per [[stop loss]], s ranges from 0.25 to 1.0.
---

## Glossary

| Symbol | Full name | Meaning |
|--------|-----------|---------|
| **X** | Entry ATR% multiple | The ATR% multiple from the 50 SMA where you buy. If X = 3, you enter when the stock is 3 × ATR%₀ above the 50 SMA. X_max is the maximum value of X that gives positive expected value. |
| **X_max** | Maximum entry multiple | The largest X where E[gain] > 0. Beyond this, the trade has negative expected value. |
| **ATR%₀** | Baseline ATR% | ATR% at the time of entry. Used as the unit of measurement for all gains and losses. ATR% = ATR(14) / Price, expressed as a percentage. |
| **ATR%(K)** | ATR% at checkpoint K | The ATR% when the stock reaches checkpoint K (7, 8, 9, 10, or 11). Expands linearly from ATR%₀ at 7× to 1.20 × ATR%₀ at 11×. Below 7×, ATR% = ATR%₀. |
| **s** | Stop distance | The initial stop is placed s × ATR%₀ below the entry price. Per [[stop loss]], s ranges from 0.25 (tight) to 1.0 (wide). The baseline uses s = 0.5. |
| **SIW** | Sell Into Weakness | The 188/1000 shares (≈20% of the position) reserved to be closed only when price undercuts the 20 EMA, per [[sell into weakness]]. |
| **E[gain]** or **E** | Expected gain per share | The average gain per share across all scenarios (failure and success), weighted by probability. Measured in units of ATR%₀ × SMA price. When E > 0, the trade has positive expected value. |
| **K** | Checkpoint number | The ATR% multiple from the 50 SMA where a sell fraction triggers. K = 7, 8, 9, 10, or 11 in the 7-11 rule. |
| **P(fail)** | Probability of failure | 70% (baseline). The chance the initial stop is hit before the stock reaches 7× ATR%. |
| **P(K+1 \| at K)** | Continuation probability | Given the stock is at K × ATR% from the 50 SMA, the probability it reaches (K+1) × ATR%. From [[denis__hamel]]'s 10-year study. |
| **1/3, 1/5, 1/8, 1/13, 1/21, 1/34** | Fibonacci denominator exit fractions | The fractions of the original position sold at each 7-11 checkpoint, per [[69 take profit rule]]. The denominators follow the Fibonacci-like sequence 3, 5, 8, 13, 21, 34. |
| **188/1000** | SIW tail | The remaining ≈18.8% of the position after all Fibonacci exits, reserved for sell into weakness. 1/3 + 1/5 + 1/8 + 1/13 + 1/21 + 1/34 ≈ 81.2%, leaving ≈18.8%. |
| **20 EMA** | 20-day exponential moving average | The SIW trigger line. Close remaining position when price closes below the 20 EMA. Rises above the 50 SMA as the stock advances. |
| **50 SMA** | 50-day simple moving average | The reference line for measuring ATR% multiples. Entry at X × ATR%₀ above the 50 SMA means the stock is X ATR-percentages above its 50-day average. |
| **(X + 1)** | 1/3 sell point | The ATR% multiple where the 1/3 is sold, always 1 above the entry. If X = 3, the 1/3 is sold at 4× ATR%₀ from the 50 SMA. |
| **0.667** | Remaining X-coefficient | The sum of all X-coefficients from the non-1/3 fractions. Since 1/3 of the position has a fixed gain of 1 (no X dependency), the remaining 2/3 has X-coefficient = −1 per share. But weighted by fraction: −1 × (2/3) = −0.667. |
| **0.200** | Total X-coefficient in EV equation | P(success) × 0.667 = 0.30 × 0.667 = 0.200. Each unit increase in X reduces expected gain by 0.200 per share. This is why X_max = constant / 0.200. |
| **0.791** | Baseline constant term | The constant term in E = 0.791 − 0.200X, computed with s = 0.5 and SIW at 50%. Represents expected gain per share at X = 0. |
