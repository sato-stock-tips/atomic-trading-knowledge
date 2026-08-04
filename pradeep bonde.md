Pradeep Bonde (@PradeepBonde / Stockbee) is a discretionary momentum swing trader whose core process-driven “trade factory” revolves around Episodic Pivots (EP and variations, including delayed reactions and 9M volume events) plus Momentum Bursts (and anticipation variants). He is not a day trader or scalper, not a long-term investor, not primarily a chart-pattern technician who draws triangles or head-and-shoulders, and not a pure value or CANSLIM trader (though IBD-style relative strength appears as one input). His edge lies in screening for strength and catalysts, studying big historical movers to interpret patterns, iteratively solving personal trading problems, and using market breadth/volume/hot-mover data to calibrate aggressiveness. He stresses imitating proven processes before innovating and that full repeatable rituals outperform chasing isolated setups. Focus is on US equities (small/mid-cap momentum/growth names, low-float or high-short-interest names, often recently public stocks under 10 years for more explosive EPs). Typical holds span days to a few weeks.60

Exact complex discretionary filters in his ritual (linear orderly prior move, shallow first/second pullback quality, “first to third setup in the move,” visual confirmation of close near high after narrow consolidation) cannot be 100% automated in any platform, including TradingView. They require chart inspection and judgment developed by reviewing big movers. Basic screens and existing community scripts approximate or directly encode large portions of the methodology with adjustable parameters. No evidence supports fully mechanical replication without user discretion on setup quality.

TradingView adaptations use the native stock screener (filters for price, volume, performance % over periods, technicals), watchlists with alerts, community Pine Script indicators (especially the protected “Pradeep Bonde (Stockbee) Momentum Pro” by vishnuv that directly implements EP tiers, momentum bursts, anticipation coiling, RS, bag-holder avoidance, and exits with dashboard/visuals/alerts), and custom Pine Script for gaps in automation.64 There is also an open-source “Stockbee Screener - Momentum Burst & Episodic Pivot Scanner.” Basic % change + volume scans (e.g., his posted 4% or the 20% in 5 days example) are straightforward natively; more precise volume multiples, narrow-range detection, or tiered scoring benefit from the scripts. Guppy MMA has community scripts. Market breadth uses index charts or custom breadth indicators.

[[episodic pivots]] (EP) — Primary/Flagship Setup

Definition (unchanged): Neglected stock (stagnant for months near lows or in dead range, low volume/interest/narrative) + sudden game-changing catalyst (earnings/sales acceleration or blowout, guidance raise, new contract/product/CEO/turnaround, sector theme, biotech approval/tie-up, inside buying, IBD highlight, or implied by extreme volume) forces violent rapid repricing (large gap or strong move on high volume, often 20-50%+ or far more in days to weeks). Three elements: Neglect + Catalyst + Rapid Repricing.

Catalysts: Themes frequently outperform pure earnings/revenue for short-term explosives. Scan for positive price reaction to catalyst, then apply anticipation or reversal entry on confirmation.

Types and TV Implementation:

- Immediate (Day 1): Strong open/gap on catalyst day.
- Delayed Reaction (Long): Messy initial move → watchlist → secondary confirmation (red-to-green, range breakout with volume, level reclaim).
- Delayed Reaction (Short): Negative catalyst → failed bounce into resistance.
- Story/Thematic or Turnaround: Theme or credible reversal catalyst.
- EP 9 Million: Extreme single-day volume spike (9M+ shares, multiples of normal; institutions active, especially smaller float). Enter on volume confirmation or next-day strength.

Screening for EP in TradingView (Native + Script): Native screener approximates single-day large moves (high 1D % change + volume spike). For full detection including tiers, delayed, quality scoring, and visuals, apply the “Pradeep Bonde (Stockbee) Momentum Pro” indicator (it flags true EP as 10%+ gap on 2x+ average volume, with Chicken/Duck/Swan/Super Swan/Golden EP tiers based on gap size, expansion, sustainability stats like 61.5% sustain rate for Super Swan; also detects delayed/PEAD).

Step-by-step native starter screen for large-move EP candidates:

1. Go to TradingView → Screener → Stocks (filter universe to US or your preferred; some plans add fundamentals like market cap).
2. Filters: Price > 5 (or > 3 variant).
3. Change % (1 Day) > 10 (or > 20 for hotter variants) or use Performance columns.
4. Volume > 300000 (or higher absolute for liquidity; combine with Avg Volume filter if available for spike).
5. Optionally add Technicals: Volume > SMA(Volume, 50) * 2 if filter supports, or high volatility.
6. Sort by 1D % Change or Volume descending.
7. Review top results for neglect signs (prior low activity on chart) and external catalyst (earnings/news tab or external search). Add to “EP Immediate Candidates” watchlist.
8. Open charts of candidates, add the Momentum Pro indicator for automated EP flagging, tier, gap risk bands, and alerts.

For 9M-style volume spikes: High absolute Volume filter + review for unusual multiples vs. recent avg (script handles better with 2x+ logic). Young IPOs (<10 years public) often more explosive — approximate via manual review or external data; low market cap (<10B) filter where available.

Entry/Exit in TradingView: Use script signals (🔥 EP or 🏆 Golden EP labels, dashboard score). Set alerts on “True Episodic Pivot,” “Gap Up,” or custom price/volume conditions. Plot or manually set stops below Day 1 low, opening range low, or structure low. Trail under swing lows or use script exit labels (first down day after 3+ ups, close below 5MA). For delayed: Add to watchlist and alert on secondary breakout or red-to-green.

Risk Notes: Script provides gap risk context (5-9% gaps often better odds) and cap-size performance differentials. Tight initial stops; scale size on high-conviction delayed entries.

Momentum Burst Setup (Complementary Short-Term Breakout)

Definition and Use: Short-term (often 3-5 day) range-expansion breakouts after consolidation, targeting 8-20%+ moves (more in hot markets). Complements EP. Your exact example screen directly matches one of his posted contexts for gauging market temperature/hot stocks indicating overall momentum strength (be more aggressive when many appear).

Exact Screen You Cited (Directly Usable in TradingView): Stocks up 20%+ in the last five days, volume 100k+, price above 5.53

Implementation in TradingView Screener:

1. Screener → Stocks.
2. Price > 5.
3. Volume > 100000 (raise for better liquidity, e.g., 300k+).
4. Performance → 5 Days > 20% (or test 8% variant for broader results).
5. Optionally: Avg Volume high or recent volume surge.
6. Sort by 5D Performance or Volume. Review list for sector clustering or breadth confirmation.
7. Add top symbols to “Hot Movers / Market Temperature” watchlist (refresh daily).
8. On charts: Add volume indicator, MA (e.g., 5/10/20), and the Momentum Pro script for automated detection of 4%+ moves on 1.4x volume with narrow-range precondition and close-near-high confirmation. Script tiers signals (Momentum Burst → Strong → Exceptional, with ★ quality prefix).

Alternative/Complementary single-day or 4%+ burst screen (common in his teachings and script):

- Change % (1 Day) > 4 (or 8/10 variants).
- Volume high (absolute or relative to avg; script uses 40%+ above average / 1.4x).
- Price > 5.
- Sort and review. Script adds narrow range before breakout + close near high filters automatically.

Ritual Criteria for Valid Momentum Burst (Discretionary Layer — Apply After Screener Pulls Candidates): Review charts manually or via script visuals:

- Breakout day close near/at high.
- Prior 3-20 day consolidation narrow or with negative bar immediately before.
- Prior price action linear/orderly (visual; avoid choppy/volatile legs).
- Breakout volume > prior day (script flags surge).
- Not up 3 consecutive days pre-breakout (bag-holder protection in script flags “LATE” after 3+ ups).
- Low-priced names (<5-10) can explode harder.
- Shallow/orderly prior pullback (first or second in rally preferred; extended rallies riskier).
- Young trend (first to third setup overall — count recent swings or use higher timeframe).
- Orderly consolidation volume (stable, not spiking prematurely).

Script approximates many of these (narrow range, close near high, quality score, bag-holder warning). Manual review remains essential for “linear” and “setup count” quality.

Entry/Exit in TradingView: Script signals (★MB or Exceptional Momentum labels, dashboard). Alerts on quality momentum burst or consolidation breakout. Exit within 3-5 days or on script exit triggers (momentum fade, below 5MA, first down after 3+ ups). Cut >8% loss in some processes. Best in strong sectors/breadth.

Anticipation Breakouts ([[ants]] / Ants+++ — Early Pre-Breakout Entries)

Definition: Early positioning in low-momentum phase before actual breakout for tighter stops and capturing more of the move. Look for coiled setups after advance.

Screening and Process in TradingView: Native: Moderate recent performance but low recent volatility/volume (harder to filter precisely; use Performance 1M or 3M moderate + low 1D/5D volatility columns if available, or volume dry-up approximation). Recommended: The Momentum Pro script detects anticipation/coiling setups (⚡ symbol for 1-3 week tight consolidations after 15-25%+ prior advance + volume <60% of average during consolidation; calculates Anticipation Score).

Step-by-step:

1. Run broad momentum or leaders screen.
2. Filter or manually select stocks in consolidation/pullback phase (not currently bursting).
3. Add to “Anticipation Coils” watchlist.
4. Apply Momentum Pro indicator — it highlights coiling with visuals and score.
5. Review for ritual: Series of narrow-range days, low volume/volatility (Bollinger squeeze visual), orderly (no 4%+ breakdowns in pullback), linear prior leg, 3-10 days consolidation, not up 3 days running, young trend (first/second pullback).
6. Set alerts on script anticipation or manual breakout levels.

Advantages in TV: Tighter stops possible; script helps quantify “coiled spring.”

Other Setups and Systems

- ADR + [[dan zanger technical trading rules]] Volume Ratio + Bull [[high tight flag]]: Approximate ADR with custom Pine or community Average True Range / Daily Range scripts. Volume ratio via script or manual. Flags via visual chart review or pattern recognition scripts. Combine with main screens.
- Trend Intensity / TI Breakouts, LTB, $ Breakout, 4% Setups, Reversals: Use % change + volume filters in screener; script covers 4%+ variants. Review charts for intensity or reversal patterns.
- CANSLIM + RS + Chart Patterns: TV has relative strength approximations or use script’s IBD-style RS (weighted recent quarters, min 80/90 rating, new high detection). Fundamentals filters where available (EPS growth in script). Patterns visual or community scripts.
- MAGNA 53+ Cap 10×10 Framework: Qualitative + screener. Market cap <10B (fundamentals filter or price proxy), high performance (YTD or multi-period), acceleration via earnings/news check (external or TV calendar). Neglect/gap/analyst elements manual or catalyst scan. IPO age manual review.
- Sugar Babies (Recurring Runners): Manual basket watchlist of ~25 names that historically deliver repeated 40-50% bursts. Scan periodically for activity or apply main burst/EP screens. Enter on volume breakout or clean pullback + strength.

Daily Processes, Rituals, and Market Temperature in TradingView

Core Philosophy Adaptation (Unchanged but Implemented): Run repeatable “trade factory.” Screen strength daily. Study 20%/50%+ movers and last week’s winners (use Performance filters or custom high-mover watchlist refreshed EOD). Solve problems from recent trades (review journal vs. charts). Use breadth/volume/hot-mover data for positioning (aggressive in strong breadth/hot conditions; defensive otherwise). Process > setups. Imitate first.

Step-by-Step Daily Ritual in TradingView:

1. Pre-Market / Open — Market Temperature & Strength Screens:

- Open Screener. Apply “Hot Movers / Market Temperature” preset (20%+ in 5D + vol/price as above, or 4%+ 1D variants).
- Early-session volume leaders: High Volume filter + positive Change % from open (review intraday).
- [[volume]]-confirmed breakouts: Change % >4 + high Volume.
- Leading stocks: YTD Performance high (e.g., >100% where filterable) + high Avg Volume.
- Sort and scan top 20-50. Note sector concentration for breadth feel.
- Add strong names to “Daily Hot / Temperature” watchlist.

3. Throughout Day: Monitor watchlists. Apply Momentum Pro indicator on key charts for live EP/MB/anticipation signals and dashboard. Set price/volume alerts. Check news/catalysts on movers.
4. EP / Catalyst Focus: High 1D change + volume spike screen → review for neglect + catalyst (TV news or external). Add to EP watchlist. Use script for tier/quality.
5. Anticipation Review: Dedicated watchlist + script coiling detection. Manual narrow-range confirmation.
6. EOD / Post-Close Study Ritual:

- Run Performance filters for 20%/50%+ movers or last-week winners watchlist.
- Review charts of big movers for patterns (what worked, failures, consolidation quality).
- Update “Leaders” or “Sugar Babies” watchlists.
- Journal problems/solutions (e.g., why a burst failed).

8. Market Trend/Breadth: Add Guppy MMA community indicator or script to major indices (SPX, etc.). Strong breadth/hot movers → more aggressive on setups. Weak → defensive, smaller size or cash.
9. Watchlist Management:

- Create dedicated folders: EP Candidates, Momentum Bursts Today, Anticipation Coils, Hot Movers/Market Temperature, YTD Leaders, Sugar Babies (manual recurring), Problem Review.
- Drag promising screener results into them.
- Use TV multi-chart layout or watchlist view with indicator applied where supported.
- Alerts: On script signals (EP, Quality MB, Coiling, RS New High, Exit, Bag Holder warning), or custom (price breaks, volume spikes, % thresholds). Alerts work on watchlist symbols.

Limitations in TV: Precise “volume 40%+ above average” or narrow-range quantification is best via the Momentum Pro script (or custom Pine). Basic screener handles % performance periods and absolute volume/price well. Full ritual quality judgment (orderly vs. choppy, exact setup count in trend) stays discretionary — review charts of screened candidates and historical big movers regularly to train eye. Script reduces some workload with visuals, scoring (PERFECT/STRONG/BUY/WATCH), and bag-holder protection (“Never buy after 3+ consecutive up days”).

Risk Management, Stops, Position Sizing, Exits (TV Implementation)

Stops are setup-specific and vary by expected move:

- Singles (8-20% in 3-5 days): Often ~8% on researched names in favorable conditions.
- Mini home runs (20-40% in weeks): Wider.
- Home runs (100%+): Wider initially. Range 2.5-20% depending on setup/catalyst/market. Script helps with structure-based stops and risk bands.52

Position sizing: 20% to 100% allocation per idea depending on conviction, catalyst strength, and conditions (concentrated on high-conviction EP or early-identified home runs; identify potential early, e.g., confident names like historical BLZE examples). Scale up on delayed/high-conviction. Use TV position size calculator or manual.

Exits: Script provides clear EXIT labels and lines. Time-based (3-5 days for bursts), momentum/volume fade, structure break, or trail (under swing lows or script 5MA). Cut losses per rules (e.g., >8% in some processes). Move to breakeven after favorable move. TV alerts on exits or key levels.

Only trade ideal conditions (strong breadth, volume support, quality setups per ritual/script score). Avoid extended or bag-holder territory (script flags).

Recommended Starting Point and Tools in TradingView

- Primary indicator: Add “Pradeep Bonde (Stockbee) Momentum Pro” (vishnuv) to charts/watchlist symbols. It encodes EP (10%+ gap 2x vol with tiers/sustainability stats), Momentum Burst (4%+ on 1.4x vol, narrow range, close near high, quality), Anticipation (coiling after advance + vol dry-up), RS (IBD-style weighted, min thresholds), bag-holder avoidance, exits, dashboard scoring, consolidation boxes, stop/target lines, and comprehensive alerts. Adjustable inputs for % thresholds, vol multiples, MA periods, etc. Matches methodology closely while adding structure.64
- Alternative open-source scanner: “Stockbee Screener - Momentum Burst & [[episodic pivots]] Scanner.”
- Native screener presets: Build and save the 20% in 5D + vol/price, 4%+ 1D + vol, high 1D change + vol spike, and YTD leaders combinations.
- Supporting: Guppy MMA script, volume profile or basic volume/MA indicators, news/calendar integration, multi-timeframe charts for trend/setup count.
- Alerts and watchlists as core workflow.
- Study: Refresh big-mover lists via Performance filters; review historical charts of past EPs/bursts.

Existing scripts and native filters make TradingView a practical platform for this methodology. Start with the Momentum Pro indicator on a core watchlist while building screener habits for daily temperature/strength. The discretionary ritual layer (setup quality, problem-solving from reviews) remains the user’s responsibility and source of edge — scripts and screens surface candidates efficiently but do not replace judgment on “orderly,” “young trend,” or overall process adherence. Specific scan syntax and exact numbers vary by implementation (script uses tuned values like 4%/1.4x and 10%/2x; original teachings reference variants including your 20% in 5 days example); backtest and adjust parameters to your universe/risk. Market conditions change — the process of studying movers and solving problems is the constant.

#gurus #screener #screening 