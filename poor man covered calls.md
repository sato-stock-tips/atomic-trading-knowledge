To execute the Adam Khoo Poor Man’s Covered Call (PMCC), you manage two distinct "legs" that serve opposite purposes. Think of the Long Leg as the house you own (the asset) and the Short Leg as the monthly rent you collect from a tenant.

Leg 1: The Long Leg
The goal of the Long Leg is to mimic owning 100 shares of the stock while using roughly 70–80% less capital.
 * Instrument: Buy to Open (BTO) Call Option.
 * Expiration (DTE): Use a LEAPS contract with 6 to 12 months (or up to 2 years) of life. This ensures that time decay (Theta) is very slow, preserving your capital.
 * Strike Price: Select a Deep In-The-Money (DITM) strike.
 * The Delta Rule: Target a Delta between 0.80 and 0.95. A 0.90 Delta means your option value moves 90% as much as the actual stock, giving you "share equivalence."
 * Intrinsic Value Check: You want the price you pay to be mostly "Intrinsic Value." The "Extrinsic Value" (the extra premium for time) should ideally be less than 10% of the total price of the option.

Leg 2: The Short Leg 
The goal of the Short Leg is to generate monthly cash flow. You are selling someone else the right to buy the stock from you at a higher price.
 * Instrument: Sell to Open (STO) Call Option.
 * Expiration (DTE): Short-term, typically 30 to 45 days. This is the "sweet spot" where time decay (Theta) accelerates, meaning the option loses value quickly so you can keep the profit.
 * Strike Price: Select an Out-of-the-Money (OTM) strike.
 * The Delta Rule: Target a Delta between 0.20 and 0.30. This strike is far enough above the current stock price that there is a high statistical probability it will expire worthless, letting you keep 100% of the rent.
 * 
The "Golden Rule" (The Safety Gap)
It is emphasized a critical mathematical check to ensure that if the stock price rockets up unexpectedly, you don't lose money.
The formula: The gap between your two strikes must be greater than the total cost you paid for the trade.
For example, if you buy a $350 Long Call and sell a $450 Short Call, your "strike gap" is $100. If you paid $85 total to enter the trade, you are safe ($100 > $85). If the stock price goes to the moon, you still profit by the difference.

Example Walkthrough (Microsoft / MSFT)
 * Long Leg Setup: If MSFT is at $420, you Buy a Jan 2027 $350 Call for $8,500. Your Delta is 0.85, meaning you effectively control 100 shares for a fraction of the $42,000 cost.
 * Short Leg Setup: You immediately Sell an April 2026 $450 Call for $350.
 * Income: You have just collected $350 in "rent."
 * Repeat: If MSFT stays below $450 by April, that short call expires worthless. You keep the $350 and sell another call for May, continuing the cycle of income.

---

#options 