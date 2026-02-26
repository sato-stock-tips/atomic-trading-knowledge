- 18-24 months period
- sell or reassess at 6 months mark
- delta close to 0.8
- strike price limited to furthest (10x ATR(14 days))% away from today's price

The Volatility Paradox: Vega and Term Structure

Vega measures the sensitivity of an option’s price to a 1% change in the implied volatility (IV) of the underlying asset, expressed as \nu = \frac{\partial C}{\partial \sigma}. LEAPS possess the highest vega in the options chain because their extended duration allows more time for volatility-driven price swings to manifest. Consequently, a rise in IV can significantly inflate the value of a LEAPS contract, even if the underlying stock remains stagnant.  

However, the tastylive framework, led by Tom Sosnoff, identifies a structural "Volatility Paradox" for LEAPS buyers. Volatility is frequently in contango, meaning that long-term IV is higher than short-term IV. A practitioner buying a LEAPS call is often paying a premium for this elevated volatility. Because IV is mean-reverting, there is a statistical 85% probability that the IV will contract over the multi-year life of the contract, creating a persistent headwind that can erode gains even in a rising market. This phenomenon explains why LEAPS often appear "misleading" to novice traders who expect to capture massive spikes in volatility that typically only impact front-month contracts.  

Temporal Erosion and the Theta Curve

Theta, or the rate of time decay (\Theta = -\frac{\partial C}{\partial \tau}), is the primary cost of carry for a LEAPS holder. The distinct advantage of LEAPS is the non-linear curvature of this decay. While short-term options lose value at an accelerating rate as they approach expiration, the decay of a LEAPS contract with two years to maturity is relatively marginal on a daily basis.  

As the contract approaches its final nine months, the theta curve begins to steepen. By the time the contract reaches 45 to 90 days until expiration (DTE), the erosion becomes aggressive. Expert management dictates exiting or rolling the position before this acceleration phase. Matt Giannino’s "6-month rule" serves as a psychological and quantitative stop-loss, forcing a liquidation or re-evaluation once the temporal advantage has been sufficiently depleted.

also see [[poor man covered calls]]

---

#options