# Stock Screener Column Configuration

## Philosophy

The screener is designed around the core trading mantra: **"Treat trading as a business where you only want the best merchandise for the shortest possible time in order to have the maximum profit with the least possible chance of failure"**

Every column serves a specific purpose in identifying high-probability [[surf trade]], [[turf trade]], and [[rebirth trade]] setups while enabling rapid decision-making at market open.

---

# screener results for stocks
## The 20 Columns (Left to Right)

 how to read the columns: those signify each individual stock, and the columns are the characteristics of the stock, which are numbered below from 1 to 20 s

### 1. Colored Flag
**Purpose:** Visual categorization and position tracking

**Color Coding:**
- **Yellow:** Currently open positions (track active trades)
- **Teal:** Surf trades watchlist (pullback to MA candidates)
- **Purple:** Turf trades watchlist (clumped MA setups)

**Why:** Instant visual separation prevents confusion between open positions and watchlist candidates. Critical for respecting [[two trades a day per market]] rule.

---

### 2. Symbol Ticker
**Purpose:** Stock identification and market detection

**Market Recognition:**
- **6 digits:** Korean stocks (KRW pricing, 1 USD = 1467 KRW)
- **4 digits:** Japan (JPY, 1 USD = 158 JPY) or Hong Kong stocks (HKD, 1 USD = 7.80 HKD)
- **Alphabets:** US stocks, occasionally Canadian (rare earth miners)

**Why:** Immediate market identification is critical for:
- Currency conversion in position size calculations
- Order type selection (IBKR midprice NOT available for Korean stocks)
- Time zone awareness (Korean Standard Time GMT+9 for opening range decisions)

---
(temporary removed for space reason)
### 3. Sector
**Purpose:** Theme identification and momentum concentration detection

**Application:**
- Hot [[sector]] create fuel for momentum
- Sector strength is a required catalyst per [[five star setups]] requirement #5
- Groups stocks for batch analysis - scan sector leaders together
- Avoid excluded sectors: Biotech and Pharmaceuticals (per [[stock screener]] filters)

**Why:** Leaders emerge from leading sectors. Sector rotation drives institutional capital flows. This column identifies where money is moving NOW.

---

### 4. Market Capitalization (USD)
**Purpose:** Liquidity assessment and institutional participation validation

**Screening Thresholds:**
- **Minimum $300M:** Episodic pivot scan requirement
- **Preferred $1B+:** RS rating scan requirement
- **$2-10B:** Sweet spot - sufficient liquidity, less efficient pricing
- **$10B+:** Allows pyramiding and larger position sizes; also candidates for [[institutional accumulation]]

**Why:**
- Validates ability to execute position sizes of $1,700-2,500 risk without slippage
- Korean/Japanese/HK markets require cap screening for spread management
- Confirms institutional-grade stocks (eliminates penny stocks and manipulation targets)

**Position Sizing Impact:**
```
Market Cap < $500M  = Reduce position size 50%
Market Cap > $2B    = Full position size acceptable
Market Cap > $10B   = Can pyramid/add to positions
```

---

### 5. Price (Note the Currency)
**Purpose:** "[[price is the best information]]" - primary data point

**Application:**
- Confirms price is respecting or reclaiming [[mark minervini (key moving averages)]]
- Entry point identification relative to EMAs/SMAs
- Validates stock meets minimum price filters:
  - [[episodic pivots]] scan: >$7
  - [[RS rating]] scan: >$12

**Why:** Price action reveals institutional intent. Price relative to moving averages determines setup type:
- Price touching EMA 10/20 = [[surf trade]] entry zone
- Price reclaiming EMA 10, EMA 20, SMA 50 simultaneously = [[turf trade]] signal
- Price above all 5 MAs after wedge pop = [[rebirth trade]] confirmation

**Currency Awareness:**
- Korean stocks in KRW (thousands to tens of thousands)
- Japanese stocks in JPY (hundreds to thousands)
- HK stocks in HKD (single digits to hundreds)
- US stocks in USD

---

### 6. Change from Open %
**Purpose:** Intraday momentum and opening range strength

**Application:**
- **+2% to +5% from open:** Strong opening range expansion (ORB candidates)
- **-1% to +1% from open:** Consolidating (watch for breakout)
- **<-2% from open:** Pullback in progress (surf trade candidates if near EMA)

**Why:**
- Identifies [[opening range breakout]] setups in real-time
- Shows if gap is holding or fading
- Reveals intraday character - strong stocks hold gaps, weak stocks fade them


**Differs from Change %:** This shows movement from TODAY's open, revealing intraday trajectory distinct from prior close comparison.

---

### 7. Gap % (1 Day)
**Purpose:** Pre-market strength and opening range breakout identification

**Application:**
- **Gap >3%:** Episodic pivot candidate, ORB setup potential
- **Gap 1-2%:** Healthy continuation gap on established trends
- **Gap <0.5%:** Normal opening (use other criteria)
- **Gap negative (gap down):** Potential shakeout entry if MA support holds

**Why:**
- [[opening range breakout]] orders require gap strength validation
- Gaps on [[surf trade]] pullback completions signal re-entry
- Gaps on [[turf trade]] MA reclaims signal explosive potential
- Pre-filters stocks BEFORE market open for preparation

**Order Type Decisions:**
```
Gap >4% + Rel Vol >2.0 = Market order (4.5+ star surf trades)
Gap 2-4% = IBKR midprice order (if not Korean stock)
Gap <2% = Opening range breakout order consideration
```

---

### 8. ATR%
**Purpose:** PRIMARY volatility filter - non-negotiable screening requirement

**Minimum Thresholds:**
- **[[surf trade]]:** ATR% >4.5%
- **[[turf trade]]:** ATR% >3.5%
- **[[rebirth trade]]:** ATR% >3.5%

**Why:**
- **Direct quote:** "Low ATR% stocks take up equity and kill time"
- Insufficient volatility cannot deliver +50% moves in short timeframes
- Determines [[stop loss]] width (high ATR% requires [[wide stops]] of 7-10%)
- Calculates extension limits: [[surf trade]] must be <4× ATR% from SMA 50 (or <5× with strong [[eps & sales]])

**Sweet Spot:** ATR% 5-12% provides explosive move potential without excessive noise shakeouts

**Critical Distinction:**
- ATR (absolute dollars) varies by price level
- ATR% normalizes across all price ranges
- A $10 stock and $200 stock both need ATR% >4.5% despite different absolute ATR values

---

### 9. Relative Volume at Time
**Purpose:** Institutional accumulation detection and volume pattern confirmation

**Application:**
- **[[surf trade]] entry:** Rel Vol <1.0 (volume dry-up on pullback) ✓
- **[[surf trade]] prior run:** Rel Vol >1.5 on up-days (accumulation) ✓
  
- **[[turf trade]] consolidation:** Rel Vol <0.8 (coiling) ✓
- **[[turf trade]] breakout:** Rel Vol >2.0 (expansion on MA reclaim) ✓
  
- **Episodic pivots:** Rel Vol >2.0+ (unusual activity)

**Why [[relative volume at time]] is Superior:**
- Compares current 5-minute bar volume to same-time average over 10 days
- Eliminates false signals from normal intraday patterns (high at open/close)
- Provides context-aware anomaly detection
- Critical for intraday decision-making

**[[five star setups]] Requirements:**
- "Big Volume in the Move Up" (#4) requires Rel Vol validation
- "Visible Accumulation" (#4) = heavy volume on up-days, quiet pullbacks

---

### 10-14. EMA 10, EMA 20, SMA 50, SMA 150, SMA 200
**Purpose:** Complete [[mark minervini (key moving averages)]] structure analysis

the numbers corresponding to the columns below is the EMA 10, EMA 20 and SMA 50, SMA 150, SMA 200 respective values, and not distances

**Setup Applications:**

**[[surf trade]] (Most Common):**
- **Strong stocks:** Price drifts to EMA 10, entry within 1 ATR%
- **Very strong stocks:** Price adheres to EMA 10 throughout run
- **Strong stocks:** Price pulls to EMA 20, entry within 1 ATR%
- **Condition:** Must be <4× ATR% from SMA 50 (avoid [[extended stocks]])

**[[turf trade]] (High Conviction):**
- **Clumped setup:** EMA 10 ≈ EMA 20 ≈ SMA 50 (all within 1-2%)
- **Entry signal:** Price reclaims all three MAs in 1-2 sessions
- **Context:** Follows [[high tight flag]] (huge run → 3-month consolidation)
- **Stop:** Below SMA 50 (entire clumped base)

**[[rebirth trade]] (Widest Stops):**
- **Wedge pop:** Price reclaims top two highest of all 5 MAs
- **Ideal:** Price sits on highest MA after pop (EMA 10 or EMA 20)
- **Structure:** All 5 MAs provide layered support
- **Confirmation:** Price above all 5 = Stage 2 advance validated

**Mark Minervini Trend Template (Rebirth Validation):**
1. Price > SMA 150 and SMA 200 ✓
2. SMA 150 > SMA 200 ✓
3. SMA 200 trending up 1+ months ✓
4. SMA 50 > SMA 150 and SMA 200 ✓
5. Price > SMA 50 ✓
6. Price >30% above 52-week low ✓
7. Price within 25% of 52-week high ✓

**Why 5 Moving Averages:**
- **EMA 10/20:** Immediate support levels, entry precision
- **SMA 50:** Extension boundary, intermediate trend
- **SMA 150/200:** Long-term trend validation, rebirth trade confirmation
- Together: Complete picture from short-term (EMA 10) to long-term (SMA 200) structure

**Visual Assessment Speed:**
```
EMA10: $45.20
EMA20: $45.00  } <1% apart = Clumping
SMA50: $44.80
Price: $46.10 = Reclaiming all 3 → TURF TRADE SIGNAL
```

---

### 15. Performance % (1 Week)
**Purpose:** Bridge between daily action and monthly trend - immediate momentum validation

**Application:**

**[[surf trade]] Weekly Rhythm:**
- **Week 1-3:** Perf (1W) +15-25% (strong power move)
- **Week 4:** Perf (1W) +5-10% (healthy consolidation begins)
- **Entry week:** Perf (1W) +2-6% while price touches EMA 10/20 = IDEAL

**[[turf trade]] Consolidation Detection:**
- **During base:** Perf (1W) compressing to +2-5% (energy coiling)
- **Breakout week:** Perf (1W) suddenly +10%+ with MA reclaim = GO

**Episodic Pivot Detection:**
- Perf (1W) >15-20% = Weekly power surge (watchlist immediately)

**Why Critical:**
- Aligns with "Weekly RSI (14) >70" filter from [[stock screener]]
- Detects the **critical power week** before healthy pullbacks
- Prevents entering stocks with weak weekly closes despite strong monthly performance
- Identifies weekly momentum shifts before they appear in monthly data

**Three-Timeframe Momentum Validation:**
```
Perf (1W): +6%   ✓ Strong weekly action
Perf (1M): +22%  ✓ Power move intact
Perf (3M): +58%  ✓ Sustained leader
= High-probability surf trade candidate
```

---

### 16. Performance % (1 Month)
**Purpose:** Recent power move detection - [[five star setups]] requirement #1

**Application:**

**Five Star Setup Filter:**
- **Minimum +20%:** Indicates institutional sponsorship
- **Preferred +30-50%:** Recent power move before consolidation
- **Exceptional +70-136%:** Epic run (setup example from knowledge base)

**Setup Classification:**
- **[[surf trade]]:** Perf (1M) +15-40% before pullback to MA
- **[[turf trade]]:** Perf (1M) moderating to +5-15% during consolidation
- **[[rebirth trade]]:** Perf (1M) +18-35% as stage 2 advance accelerates

**Why Required:**
- "Strong Momentum First" - [[five star setups]] #1
- "Weak names don't magically become leaders"
- Confirms stock already proved institutional interest through price movement
- 1-month window captures recent accumulation without being too short-term

**Combined with Perf (1W):**
```
Perf (1W): +3%, Perf (1M): +25% = Healthy pullback in strong trend ✓
Perf (1W): +15%, Perf (1M): +18% = Recent acceleration, watch for base ✓
Perf (1W): -2%, Perf (1M): +5% = Weak trend, avoid ✗
```

---

### 17. Performance % (3 Months)
**Purpose:** Sustained trend validation - separates real leaders from flash moves

**Application:**

**Trend Quality Assessment:**
- **Perf (3M) >40%:** Established uptrend with institutional backing
- **Perf (3M) >70%:** Epic trend (high tight flag potential)
- **Perf (3M) +20-35%:** Steady advance (rebirth trade context)

**Setup Validation:**

**[[surf trade]]:**
```
Perf (3M): +55%  → Established leader
Perf (1M): +22%  → Recent strength
Perf (1W): +6%   → Healthy weekly action
= Confirmed sustained momentum, not flash spike
```

**[[turf trade]]:**
```
Perf (3M): +85%  → Prior huge run ([[high tight flag]] requirement)
Perf (1M): +8%   → Now consolidating (base forming)
EMA10≈EMA20≈SMA50 → MAs clumping during consolidation
= Classic turf trade setup emerging
```

**[[rebirth trade]]:**
```
Perf (3M): +35%  → Stage 2 advance underway
Perf (1M): +18%  → Accelerating
Price > all 5 MAs → Wedge pop confirmed
= Rebirth gaining momentum
```

**Why 3 Months Specifically:**
- [[high tight flag]] patterns typically form over 2-3 month consolidations
- Sufficient to validate institutional commitment
- Not so long that trend becomes stale
- Aligns with "shortest possible time" philosophy while ensuring quality

**Risk Filter:**
```
Perf (3M): +60%, Perf (1M): +5%  = Trend weakening, avoid
Perf (3M): +15%, Perf (1M): +25% = Late acceleration, risky
Perf (3M): +50%, Perf (1M): +20% = Sustained strength, ideal ✓
```

---

### 18. Performance % (6 Months)
**Purpose:** Long-term trend context and [[rebirth trade]] validation

**Application:**

**[[rebirth trade]] Confirmation:**
- Stage 2 advances often develop over 4-6+ months
- Validates recovery from Stage 1 base
- Confirms NOT just short-term bounce

**Trend Maturity Assessment:**
```
Perf (6M): +120%, Perf (3M): +45%, Perf (1M): +12%
= Mature trend, gains decelerating, risk increases

Perf (6M): +45%, Perf (3M): +55%, Perf (1M): +25%
= Accelerating trend, ideal for surf/turf entries ✓

Perf (6M): +15%, Perf (3M): +40%, Perf (1M): +22%
= Early stage momentum, rebirth trade potential ✓
```

**Four-Timeframe Momentum Cascade:**
```
Perf (6M): +85%  → Long-term leader
Perf (3M): +60%  → Trend intact
Perf (1M): +22%  → Recent strength
Perf (1W): +5%   → Healthy pullback
= Complete momentum picture from weekly to 6-month trend
```

**Why Include:**
- Provides complete trend context from 1 week to 6 months
- Essential for [[rebirth trade]] identification (requires long-term structure)
- Reveals if stock is early-stage (accelerating) vs late-stage (decelerating)
- Complements shorter timeframes without being too historical

**Mark Minervini Trend Template Connection:**
- "Price at least 30% above 52-week low"
- "Preferably 100%, 300%+ above 52-week low before consolidation"
- Perf (6M) often captures this metric

---

### 19. EPS Diluted Growth (TTM YoY)
**Purpose:** Earnings catalyst validation - [[five star setups]] requirement #6

**Threshold from [[five star setups]]:**
- **Minimum +50% YoY:** Five star setup requirement
- **Preferred +60%+:** Strong fundamental momentum
- **Exceptional +100%+:** Explosive growth story

**Application:**

**Five Star Quality Filter:**
```
EPS Growth +65%, Revenue Growth +28% = Real organic growth ✓ (5-star)
EPS Growth +45%, Revenue Growth +22% = Good fundamentals ✓ (4-star)
EPS Growth +25%, Revenue Growth +8%  = Weak fundamentals (3-star max)
```

**Setup Integration:**
- **[[surf trade]]:** EPS +50%+ allows tolerance for 5× ATR% extension from SMA 50 (vs 4× standard)
- **[[turf trade]]:** Strong EPS validates consolidation = accumulation, not distribution
- **[[rebirth trade]]:** EPS acceleration confirms Stage 2 advance has fundamental backing

**Why TTM (Trailing Twelve Months):**
- Smooths quarterly volatility
- More reliable than forward estimates (eliminates management guidance games)
- Shows actual delivered results, not promises

**Part of [[eps & sales]]:**
- Both EPS and Revenue growth required for complete fundamental picture
- See column #20 for revenue validation

---

### 20. Revenue Growth (TTM YoY)
**Purpose:** Sales momentum validation - completes [[eps & sales]] fundamental analysis

**Threshold from [[five star setups]]:**
- **Minimum +20% YoY:** Five star setup requirement
- **Preferred +25%+:** Strong top-line growth
- **Exceptional +40%+:** Hyper-growth story

**Why Revenue + EPS Together:**

**Quality Assessment Matrix:**

| EPS Growth | Revenue Growth | Interpretation | Action |
|------------|----------------|----------------|---------|
| +60% | +30% | Organic growth + margin expansion | ✓ BEST (5-star quality) |
| +50% | +22% | Real growth story | ✓ GOOD (4-star acceptable) |
| +80% | +8% | Financial engineering (buybacks) | ⚠ WEAK (lower conviction) |
| +45% | +45% | Pure organic growth | ✓ GOOD (clean story) |
| +30% | -5% | Unsustainable (cost cutting only) | ✗ REJECT |

**Setup Applications:**

**[[surf trade]] Extension Tolerance:**
```
Standard: <4× ATR% from SMA 50
With EPS +50% AND Revenue +20%: <5× ATR% acceptable
= Strong fundamentals justify slightly extended entries
```

**[[turf trade]] Consolidation Validation:**
```
EPS +65%, Revenue +28% during consolidation
= Fundamentals improving while price digests
= Accumulation pattern, NOT distribution ✓
```

**[[rebirth trade]] Turnaround Confirmation:**
```
Recent quarters showing revenue acceleration
= True rebirth, not just accounting tricks
= Stage 2 advance has fundamental fuel ✓
```

**Sector Context:**
- **Tech/Growth:** Revenue growth often leads EPS (invest in growth phase)
- **Mature Companies:** EPS growth > Revenue growth (margin expansion story)
- **Korean/Asian Markets:** Revenue growth more reliable metric (less accounting flexibility)

**Part of [[eps & sales]] Knowledge:**
- Indicator shows both figures as overlays on earnings report candles
- Both metrics required for [[five star setups]] catalyst requirement
- "Structural story" (rare earth materials, AI theme) can substitute if fundamentals lag

---

## Column Synergies - How They Work Together

### Opening Range Breakout (ORB) Decision Tree


```
Step 1: Filter by Gap % >2%
Step 2: Check Rel Vol >1.5 (confirming gap)
Step 3: Verify Change from Open % >0% (gap holding)
Step 4: Confirm ATR% >4.5% (sufficient volatility)
Step 5: Check Price vs EMA 10 (near support or breaking out?)
Step 6: Validate Perf (1W) >3% (weekly momentum)

Decision:
- All conditions met + 4.5-5 star setup = Market order
- Most conditions met + Korean stock = Opening range breakout order (no midprice available)
- Most conditions met + US stock = IBKR midprice order
```

### Surf Trade Identification Pattern
```
Sector: Hot theme (Semiconductors, AI, Battery) ✓
Perf (6M): +70% → Long-term leader
Perf (3M): +50% → Sustained trend
Perf (1M): +20% → Recent power move
Perf (1W): +4%  → Healthy weekly action
Change from Open %: -1.2% → Pullback day
Price: $45.20, EMA 10: $44.90 → Within 0.7% (under 1 ATR%)
ATR%: 5.3% ✓
Rel Vol: 0.65 → Volume dry-up ✓
SMA 50: $42.10 → Price 7.4% above (1.4× ATR%, well under 4× limit) ✓
EPS: +58%, Revenue: +26% ✓

Rating: 4.5-star surf trade
Entry: $45.20, Stop: $44.50 (below EMA 10)
Risk: $2,000 (4.5-star), Shares: $2,000 ÷ $0.70 = 2,857 shares
```

### Turf Trade Identification Pattern
```
Sector: Hot theme ✓
Perf (6M): +95% → Epic prior run
Perf (3M): +85% → High tight flag context
Perf (1M): +8%  → Consolidating (base forming)
Perf (1W): +11% → Weekly breakout starting!
Price: $53.00
EMA 10: $52.20  } All within 1.5% = CLUMPED ✓
EMA 20: $52.10  }
SMA 50: $51.80  }
Change from Open %: +2.8% → Strong session
Rel Vol: 2.3 → Volume expansion on MA reclaim ✓
ATR%: 4.9% ✓
EPS: +68%, Revenue: +31% ✓

Rating: 5-star turf trade
Entry: $53.10 (reclaimed all 3 MAs), Stop: $51.50 (below SMA 50)
Risk: $2,500 (5-star), Shares: $2,500 ÷ $1.60 = 1,562 shares
Order: Market order (5-star setup, gap holding, high conviction)
```

### Rebirth Trade Identification Pattern
```
Perf (6M): +38% → Stage 2 advance developing
Perf (3M): +35% → Consistent climb
Perf (1M): +18% → Accelerating
Price: $78.00
EMA 10: $76.50 ✓ Above
EMA 20: $74.20 ✓ Above
SMA 50: $71.00 ✓ Above (wedge pop = reclaimed top 2 of 5 MAs)
SMA 150: $68.50 ✓ Above
SMA 200: $66.20 ✓ Above all 5 MAs = Stage 2 confirmed
ATR%: 3.9% ✓ (meets 3.5% minimum)
Revenue: +32% → Fundamental turnaround ✓
Market Cap: $4.2B ✓ Institutional size

Rating: 4-star rebirth trade
Entry: $78.20, Stop: $73.50 (below SMA 50, wider stop acceptable)
Risk: $1,700 (4-star), Shares: $1,700 ÷ $4.70 = 361 shares
```

---

## Speed Advantages of This Configuration

### Pre-Market Preparation (Before Korean Market Open)
1. **Sort by Gap %** descending
2. **Filter ATR% >4.5%** (instant quality filter)
3. **Scan Sector** column for hot themes
4. **Check Perf (1W), (1M), (3M)** cascade for momentum quality
5. **Prepare ORB orders** for top 5-10 candidates

**Time to identify top candidates: 2-3 minutes**

### Intraday Pullback Scan
1. **Filter:** Change from Open % between -2% and +0.5%
2. **Filter:** Rel Vol <1.0 (volume dry-up)
3. **Filter:** ATR% >4.5%
4. **Check Price vs EMA 10/20** (within 1 ATR%?)
5. **Validate Perf (1W), (1M)** still strong

**Time to find surf trade entries: 1-2 minutes**

### Position Sizing Calculation
**All required data in one row:**
```
Price: $45.20 (KRW ₩66,300)
ATR%: 5.3% → ATR ≈ $2.40
Stop: Below EMA 10 at $44.50 (KRW ₩65,300)
Risk per share: $0.70
Setup: 4.5-star → Risk $2,000
Shares: $2,000 ÷ $0.70 = 2,857 shares
Market Cap: $2.1B → Sufficient liquidity ✓
```

**Time to calculate position size: 30 seconds**

---

# screener results for [[sector]]

from left to right

1. symbol
2. name
3. focus
4. price
5. change%
6. gap%
7. ATR%(14)
8. relative volume at time
9. ema(10)
10. ema(20)
11. sma(50)
12. sma(150)
13. sma(200)
14. performance (perf 1 week)
15. performance (perf 1 months)
16. performance (perf 3 months)
17. performance (perf 6 months)

if in doubt, read above for how each of these numbered columns work



---




## Integration with Other Knowledge

This screener configuration integrates with:

- **[[five star setups]]:** Columns 15-20 validate all 8 requirements
- **[[surf trade]]:** Columns 10-14 (MAs) + 9 (Rel Vol) + 8 (ATR%) enable precise entries
- **[[turf trade]]:** Columns 10-12 (MA clumping) + 15-17 (performance consolidation) identify setups
- **[[rebirth trade]]:** Columns 10-14 (all 5 MAs) + 18 (6M performance) validate Stage 2
- **[[opening range breakout]]:** Columns 6-7 (Gap %, Change from Open %) + 9 (Rel Vol) enable ORB orders
- **[[stop loss]]:** Columns 8 (ATR%) + 10-12 (EMAs/SMA 50) define stop placement
- **[[position size]]:** Columns 4-5 (Price) + 8 (ATR%) + 3 (Market Cap) enable risk calculation
- **[[two trades a day per market]]:** Column 2 (Ticker) identifies market/currency for limit enforcement
- **[[stock screener]]:** Validates episodic pivot and RS rating scan requirements
- **[[eps & sales]]:** Columns 19-20 provide fundamental catalyst confirmation
- **[[market trend model]]:** (Green markets assumed unless stated; screener focuses on stock selection within favorable markets)

---

## Best Practices

### Daily Workflow

**Pre-Market (30 min before open):**
1. Sort by Gap % (descending)
2. Review teal flags (surf watchlist) - check if pullbacks complete
3. Review purple flags (turf watchlist) - check if MA reclaims occurring
4. Prepare [[opening range breakout]] orders for top 3-5 gappers
5. Set alerts for key price levels (EMA touches)

**First Hour (Opening range):**
1. Execute ORB orders or market orders on 5-star setups
2. Monitor Change from Open % and Rel Vol for confirmation
3. Respect [[two trades a day per market]] limit

**Mid-Session:**
1. Filter Change from Open % (-2% to +0.5%) for pullback entries
2. Check Rel Vol <1.0 for volume dry-up
3. Enter surf trades at EMA 10/20 touches with position sizing per setup rating

**End of Day:**
1. Update colored flags based on new positions
2. Review Perf (1W) for weekly momentum shifts
3. Scan for new turf trade candidates (look for MA compression in Columns 10-12)

### Maintenance

**Weekly:**
- Remove stocks from watchlist if ATR% drops below minimums
- Remove stocks if Perf (1M) turns negative (broken trend)
- Add new episodic pivots (Gap % >7.5% + Rel Vol >2.0)

**Monthly:**
- Review Perf (3M) to validate sustained leaders
- Check for sector rotation (hot sectors changing)
- Update fundamental data (EPS/Revenue growth quarterly)

---

## Why This Configuration Works

**Alignment with Philosophy:**

**"Best Merchandise":**
- Sector + Market Cap + ATR% + Performance metrics = Quality filter
- EPS/Revenue Growth = Fundamental validation
- Only leaders with institutional backing pass all filters

**"Shortest Possible Time":**
- Gap % + Change from Open % = Intraday opportunities
- Perf (1W) = Weekly momentum for quick moves
- ATR% >4.5% = Volatility for explosive gains

**"Maximum Profit":**
- EMAs/SMAs = Precise entries at optimal support
- Rel Vol = Accumulation confirmation
- Performance cascade (1W/1M/3M/6M) = Trend riding potential

**"Least Possible Chance of Failure":**
- ATR% = Proper position sizing
- Market Cap = Sufficient liquidity (no slippage)
- EPS/Revenue = Fundamental safety net
- Multiple MAs = Layered support structure

This screener transforms abstract trading principles into actionable, visual data for rapid decision-making at market speed.

---

#screening #stock-selection #screener-columns #setup #surf-trade #turf-trade #rebirth-trade #configuration