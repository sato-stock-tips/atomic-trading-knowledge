
### on individual stocks

6-9 take profit rule refers to the stock price [[ATR%]] multiple away from the 50 SMA. The rule **only activates when the stock reaches 6x ATR% above the 50-day SMA**.
**

When the stock is at 6x ATR percentage from the 50-day SMA, sell one quarter of the current position size. Do the same at 7,8,9x.


1/3 is sold 3-5 days later

**After all specified sales, you will have approximately 210 shares left out of the original 1,000.**

Which is close to letting the remaining 20% run

---

the above was inspired by: https://x.com/justrandomguy_k/status/2058819629192036579?s=46

---

Research done
by @denis__hamel

How Often Do Stocks Hit 7-12+ ATR Above SMA50?

A 10-Year Study (Corrected, Filtered)

A follow-up to my September 24 thread, with three upgrades: the standard formula for ATR-distance, per-leg observation instead of per-day, and a filter that removes gap-events and post-event "pinning" (acquisitions, FDA approvals, post-bankruptcy stabilization). The headline takeaways are unchanged - trim hard at 7, hold a small runner above 11 - but the sizing math is much sharper.

TL;DR

For any US stock that reaches 7 ATR above its 50-day SMA in a sustained run, the right scale-out is approximately:

[

](https://x.com/denis__hamel/article/2056088634382537028/media/2056081621128740865)

This is remarkably stable across liquidity tiers - from heavily-traded names (

[$50M+](https://x.com/search?q=%2450M%2B&src=cashtag_click)

/day) down to lightly-traded names (

[$1-10M](https://x.com/search?q=%241-10M&src=cashtag_click)

/day). The only meaningful difference: lightly-traded names deserve a slightly bigger runner because their right tail is fatter, even after filtering.

Scope of the study

• Universe: 5,998 US common stocks (TradingView screener - close > $3, type = stock, typespecs = common, excluding pre-IPO) • Period: ~10 years of daily bars (2016 -> 2026) • Stock-days analyzed: 10,137,895 • Total legs above SMA50: 403,307 • After CLEAN filter: 382,252 legs (98.0% retained) • CLEAN legs reaching >= 7 ATR: 3,806 • Reference: SMA(50) + ATR(14) • Formula: (Close - SMA50) / ATR - the standard "ATRs above SMA50"

A "leg" is a continuous span where Close > SMA50. Each leg contributes one observation - its peak ATR-distance - to the histogram. This avoids the double-counting you get when a stock sits at 8 ATR for two weeks and inflates "stock-days at 8 ATR" by 14.

The CLEAN filter removes two types of artifact: (1) gap-events - any leg containing a single day with |Close/PrevClose - 1| > 25%, catching things like biotech FDA-approval gaps, reverse-merger pops, and pennystock pumps; and (2) pinning - any leg where the smallest 20-bar in-leg ATR% falls below 40% of the 30-bar pre-leg ATR%, catching acquisition deals where the stock trades flat at the deal price while SMA50 slowly catches up.

How widespread are extreme extensions?

Of the 5,998 tradable US common stocks in the universe, here is how many ever reached each level at least once over the 10-year window (CLEAN legs only):

[

](https://x.com/denis__hamel/article/2056088634382537028/media/2056081820878303232)

Three out of every five tradable US common stocks have had at least one genuine >=7 ATR move in the past decade. Two-fifths hit 8. A quarter hit 9. By the time you are talking about 15+ ATR, you are in the 1.2% tail. 20+ is essentially never - just 4 names across the entire 10-year window.

Compared to the unfiltered numbers: 74.6% became 61.9% at 7+, 24.7% became 16.6% at 10+, and 0.3% became 0.1% at 20+. The filter removes about 17% of the "ever reached" stock counts - those were the ones whose readings were artifacts.

Per-liquidity breakdown

Why dollar volume, not market cap. I would have preferred to slice by market cap (true large/mid/small) but I do not have historical market cap series - only today's. Filtering legs from 2018 by a 2026 market-cap snapshot is a textbook survivorship bias: a stock that is $30B today was $2B back then, so it would land in the wrong bucket; a stock that went to zero in 2020 would not appear at all.

Dollar volume (DollarVolSma20, the 20-day average of price x volume) is computed per bar from the historical OHLCV stream, so it reflects what the stock looked like on the day the leg started - point-in-time, no survivorship contamination. It is a liquidity proxy rather than a size proxy. The two correlate (large caps are usually heavily traded), but they're not identical: a sleepy $5B mid-cap with low turnover can read "Light" while a hot $500M small-cap during a news event reads "Heavy". Read the tiers as "what kind of fill could you actually get on this name on that day," not "what was its market cap."

Three tiers, measured at the leg's start bar:

• Heavy —

[$50M+](https://x.com/search?q=%2450M%2B&src=cashtag_click)

average daily dollar volume - "heavily traded" • Medium — $10M to $50M - "moderately traded" • Light — $1M to $10M - "lightly traded"

Legs with dollar volume below $1M are excluded entirely (untradeable for any meaningful retail size).

Heavy -

[$50M+](https://x.com/search?q=%2450M%2B&src=cashtag_click)

avg dollar volume (heavily traded)

[

](https://x.com/denis__hamel/article/2056088634382537028/media/2056082009810673664)

Heavily-traded names have the tightest right tail. After filtering, only 4 such legs in 10 years made it past 11 ATR. The runner allocation is mathematically tiny.

Medium -

[$10M-](https://x.com/search?q=%2410M-&src=cashtag_click)

$50M avg dollar volume (moderately traded)

[

](https://x.com/denis__hamel/article/2056088634382537028/media/2056082199762354176)

Moderately-traded names behave almost identically to heavily-traded ones once they are moving. Same sell distribution, same tail behavior. If you trade both, you do not need separate rules.

Light -

[$1M-](https://x.com/search?q=%241M-&src=cashtag_click)

$10M avg dollar volume (lightly traded)

[

](https://x.com/denis__hamel/article/2056088634382537028/media/2056082243378982912)

Lightly-traded names have the fattest right tail even after the filter - 14 legs reached 11+ ATR vs 3-4 for the higher-volume tiers. This is the genuine fat-tail of low-liquidity momentum, not pennystock pump artifacts (those were filtered). The runner allocation should be slightly larger here to capture them.

The continuation probability angle

Another way to frame the same data: given you are at level K, what is the probability the leg pushes to K+1?

[

](https://x.com/denis__hamel/article/2056088634382537028/media/2056082326434525184)

Three things jump out:

1. The drop from "at 7" to "reaching 8" is sharp - roughly two-thirds of legs that touch 7 die there. That is the math behind the 65% trim at first touch.

2. Heavy and Medium volume tiers hit a wall at 11 - zero of those legs continued past 11 ATR in the clean dataset. The deepest "real" momentum tail lives in lightly-traded names.

3. Lightly-traded names keep climbing even in the clean dataset - 42.9% continuation from 11 to 12. Real low-liquidity momentum survives the filter.

The receipts: every Heavy leg that reached 11+ ATR

After the filter, only 4 heavily-traded (

[$50M+](https://x.com/search?q=%2450M%2B&src=cashtag_click)

avg dollar volume) legs across 10 years made it past 11 ATR. These are the genuine momentum runs - no acquisitions, no FDA gaps, no penny pumps.

[

](https://x.com/denis__hamel/article/2056088634382537028/media/2056081263602139136)

Note: Start = first day Close > SMA50. End = day Close fell back under SMA50. The peak ATR happens INSIDE the leg, so Start and End are both near SMA50 by definition. Move-to-peak for these four: STLA +64%, EME +86%, CM +25%, PVH +83%.

# 

Where your favorite stocks fit on the scale

Names everyone on FinTwit watches, ranked by their highest CLEAN peak ATR over the 10-year window. Use this as a reality check: the framework above is calibrated on real moves you remember.

  

[

](https://x.com/denis__hamel/article/2056088634382537028/media/2056084641740660736)

A few notes on this table: TSLA's 10.21 ATR was the COVID rally into early 2020. AAPL's 10.11 came in the iPhone X cycle, late 2016 - mid 2017. NFLX's 10.02 was the pre-streaming-wars dominance run of early 2018. NVDA's 8.94 was the famous AI bubble leg (Nov 2023 - Apr 2024). The fact that even the most-watched, most-loved momentum names rarely break 10 ATR is the strongest argument for trimming aggressively when one of your own positions gets there.

What the filter caught

In the original unfiltered dataset, 10 heavily-traded legs reached 11+ ATR. The filter removed 6 of them as artifacts:

Symbol Why filtered Reading

SLNO gap-event + pinning Biotech FDA approval, then drifts OPLN gap-event Single-day +181% move HTZ pinning Post-bankruptcy stabilization MASI pinning Apple settlement, trades flat CPRI pinning Tapestry tender offer UGRO gap-event Pennystock pump (+417% in a day)

The filter is not arbitrary - it was calibrated against this exact 10-leg sample. Across the full 225,215-leg tradable dataset, it removes ~2% of legs (4,446 of 225,215): 3,605 gap-events, 892 pinned, 51 both. The removals concentrate in extreme-extension bins - which is the whole point.

Why these numbers differ from my September thread

Three changes:

1. Per-leg observation, not per-day. My original thread reported stock-days at each ATR level. That double-counts: a stock that sits at 8 ATR for two weeks adds 14 to the "8 ATR" bucket. Per-leg counts each move once.

2. Standard formula. My original thread used ((Close/SMA50) - 1) / (ATR/Close). Algebraically that equals (Close - SMA50) / ATR x (Close / SMA50) - the extra Close/SMA50 factor inflated readings on extended names. A stock truly at 8 ATR above SMA50 read as 11.2 ATR; a stock at 2x its SMA50 read at double its actual ATR-count.

3. Gap + pinning filter. Acquisitions (CPRI, MASI), regulatory events (SLNO, HTZ), and pennystock pumps (UGRO, OPLN) generate "extensions" that are not real trends. The CLEAN dataset removes them based on two signals - large single-day returns and post-event volatility collapse.

Net effect: tighter rule, fewer false runners, more accurate sizing - the headline ("trim hard at 7, runner above 11") is unchanged in spirit but the magnitudes now reflect what is actually tradable, not what a takeover or biotech-gap algorithm sees.

The framework

The sizing logic is:

Sell at le
Denis Hamel
@denis__hamel
How Often Do Stocks Hit 7-12+ ATR Above SMA50?
A 10-Year Study (Corrected, Filtered)
A follow-up to my September 24 thread, with three upgrades: the standard formula for ATR-distance, per-leg observation instead of per-day, and a filter that removes gap-events and post-event "pinning" (acquisitions, FDA approvals, post-bankruptcy stabilization). The headline takeaways are unchanged - trim hard at 7, hold a small runner above 11 - but the sizing math is much sharper.
TL;DR
For any US stock that reaches 7 ATR above its 50-day SMA in a sustained run, the right scale-out is approximately:
 This is remarkably stable across liquidity tiers - from heavily-traded names ($50M+/day) down to lightly-traded names ($1-10M/day). The only meaningful difference: lightly-traded names deserve a slightly bigger runner because their right tail is fatter, even after filtering.
Scope of the study
•	Universe: 5,998 US common stocks (TradingView screener - close > $3, type = stock, typespecs = common, excluding pre-IPO)
•	Period: ~10 years of daily bars (2016 -> 2026)
•	Stock-days analyzed: 10,137,895
•	Total legs above SMA50: 403,307
•	After CLEAN filter: 382,252 legs (98.0% retained)
•	CLEAN legs reaching >= 7 ATR: 3,806
•	Reference: SMA(50) + ATR(14)
•	Formula: (Close - SMA50) / ATR - the standard "ATRs above SMA50"
A "leg" is a continuous span where Close > SMA50. Each leg contributes one observation - its peak ATR-distance - to the histogram. This avoids the double-counting you get when a stock sits at 8 ATR for two weeks and inflates "stock-days at 8 ATR" by 14.
The CLEAN filter removes two types of artifact: (1) gap-events - any leg containing a single day with |Close/PrevClose - 1| > 25%, catching things like biotech FDA-approval gaps, reverse-merger pops, and pennystock pumps; and (2) pinning - any leg where the smallest 20-bar in-leg ATR% falls below 40% of the 30-bar pre-leg ATR%, catching acquisition deals where the stock trades flat at the deal price while SMA50 slowly catches up.
How widespread are extreme extensions?
Of the 5,998 tradable US common stocks in the universe, here is how many ever reached each level at least once over the 10-year window (CLEAN legs only):
 Three out of every five tradable US common stocks have had at least one genuine >=7 ATR move in the past decade. Two-fifths hit 8. A quarter hit 9. By the time you are talking about 15+ ATR, you are in the 1.2% tail. 20+ is essentially never - just 4 names across the entire 10-year window.
Compared to the unfiltered numbers: 74.6% became 61.9% at 7+, 24.7% became 16.6% at 10+, and 0.3% became 0.1% at 20+. The filter removes about 17% of the "ever reached" stock counts - those were the ones whose readings were artifacts.
Per-liquidity breakdown
Why dollar volume, not market cap. I would have preferred to slice by market cap (true large/mid/small) but I do not have historical market cap series - only today's. Filtering legs from 2018 by a 2026 market-cap snapshot is a textbook survivorship bias: a stock that is $30B today was $2B back then, so it would land in the wrong bucket; a stock that went to zero in 2020 would not appear at all.
Dollar volume (DollarVolSma20, the 20-day average of price x volume) is computed per bar from the historical OHLCV stream, so it reflects what the stock looked like on the day the leg started - point-in-time, no survivorship contamination. It is a liquidity proxy rather than a size proxy. The two correlate (large caps are usually heavily traded), but they're not identical: a sleepy $5B mid-cap with low turnover can read "Light" while a hot $500M small-cap during a news event reads "Heavy". Read the tiers as "what kind of fill could you actually get on this name on that day," not "what was its market cap."
Three tiers, measured at the leg's start bar:
•	Heavy — $50M+ average daily dollar volume - "heavily traded"
•	Medium — $10M to $50M - "moderately traded"
•	Light — $1M to $10M - "lightly traded"
Legs with dollar volume below $1M are excluded entirely (untradeable for any meaningful retail size).
Heavy - $50M+ avg dollar volume (heavily traded)
 
Heavily-traded names have the tightest right tail. After filtering, only 4 such legs in 10 years made it past 11 ATR. The runner allocation is mathematically tiny.
Medium - $10M-$50M avg dollar volume (moderately traded)
 
Moderately-traded names behave almost identically to heavily-traded ones once they are moving. Same sell distribution, same tail behavior. If you trade both, you do not need separate rules.
Light - $1M-$10M avg dollar volume (lightly traded)
 
Lightly-traded names have the fattest right tail even after the filter - 14 legs reached 11+ ATR vs 3-4 for the higher-volume tiers. This is the genuine fat-tail of low-liquidity momentum, not pennystock pump artifacts (those were filtered). The runner allocation should be slightly larger here to capture them.
The continuation probability angle
Another way to frame the same data: given you are at level K, what is the probability the leg pushes to K+1?
 
Three things jump out:
1.	The drop from "at 7" to "reaching 8" is sharp - roughly two-thirds of legs that touch 7 die there. That is the math behind the 65% trim at first touch.
2.	Heavy and Medium volume tiers hit a wall at 11 - zero of those legs continued past 11 ATR in the clean dataset. The deepest "real" momentum tail lives in lightly-traded names.
3.	Lightly-traded names keep climbing even in the clean dataset - 42.9% continuation from 11 to 12. Real low-liquidity momentum survives the filter.
The receipts: every Heavy leg that reached 11+ ATR
After the filter, only 4 heavily-traded ($50M+ avg dollar volume) legs across 10 years made it past 11 ATR. These are the genuine momentum runs - no acquisitions, no FDA gaps, no penny pumps.
 
Note: Start = first day Close > SMA50. End = day Close fell back under SMA50. The peak ATR happens INSIDE the leg, so Start and End are both near SMA50 by definition. Move-to-peak for these four: STLA +64%, EME +86%, CM +25%, PVH +83%.
Where your favorite stocks fit on the scale
Names everyone on FinTwit watches, ranked by their highest CLEAN peak ATR over the 10-year window. Use this as a reality check: the framework above is calibrated on real moves you remember.

A few notes on this table: TSLA's 10.21 ATR was the COVID rally into early 2020. AAPL's 10.11 came in the iPhone X cycle, late 2016 - mid 2017. NFLX's 10.02 was the pre-streaming-wars dominance run of early 2018. NVDA's 8.94 was the famous AI bubble leg (Nov 2023 - Apr 2024). The fact that even the most-watched, most-loved momentum names rarely break 10 ATR is the strongest argument for trimming aggressively when one of your own positions gets there.
What the filter caught
In the original unfiltered dataset, 10 heavily-traded legs reached 11+ ATR. The filter removed 6 of them as artifacts:
Symbol	Why filtered	Reading
SLNO	gap-event + pinning	Biotech FDA approval, then drifts
OPLN	gap-event	Single-day +181% move
HTZ	pinning	Post-bankruptcy stabilization
MASI	pinning	Apple settlement, trades flat
CPRI	pinning	Tapestry tender offer
UGRO	gap-event	Pennystock pump (+417% in a day)
The filter is not arbitrary - it was calibrated against this exact 10-leg sample. Across the full 225,215-leg tradable dataset, it removes ~2% of legs (4,446 of 225,215): 3,605 gap-events, 892 pinned, 51 both. The removals concentrate in extreme-extension bins - which is the whole point.
Why these numbers differ from my September thread
Three changes:
1. Per-leg observation, not per-day. My original thread reported stock-days at each ATR level. That double-counts: a stock that sits at 8 ATR for two weeks adds 14 to the "8 ATR" bucket. Per-leg counts each move once.
2. Standard formula. My original thread used ((Close/SMA50) - 1) / (ATR/Close). Algebraically that equals (Close - SMA50) / ATR x (Close / SMA50) - the extra Close/SMA50 factor inflated readings on extended names. A stock truly at 8 ATR above SMA50 read as 11.2 ATR; a stock at 2x its SMA50 read at double its actual ATR-count.
3. Gap + pinning filter. Acquisitions (CPRI, MASI), regulatory events (SLNO, HTZ), and pennystock pumps (UGRO, OPLN) generate "extensions" that are not real trends. The CLEAN dataset removes them based on two signals - large single-day returns and post-event volatility collapse.
Net effect: tighter rule, fewer false runners, more accurate sizing - the headline ("trim hard at 7, runner above 11") is unchanged in spirit but the magnitudes now reflect what is actually tradable, not what a takeover or biotech-gap algorithm sees.
The framework
The sizing logic is:
Sell at level K = the probability that this leg's peak lands at K (given it reached >= 7).
The histogram of leg peaks IS the scale-out distribution. By construction it sums to 100%, and each fraction is the unconditional probability of "the move ends here." Selling proportionally captures the expected harvest at each level without leaving anything in undefined territory.
This also explains why the rule is stable across tiers: the shape of the peak distribution is similar (steep dropoff after 7, very thin tail past 11), even though the absolute number of legs differs by 10x between Heavy and Light. The percentages - what matters for sizing - barely move.

---
linked to AI study: [[7-11 rule maximum entry ATR% multiple]]