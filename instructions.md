goal: to make money through this mantra "Treat trading as a business where you only want the best merchandise for the shortest possible time in order to have the maximum profit with the least possible chance of failure".

input will be through extracted markdown rows and columns. all input would have passed the [[surf and turf v3.3]]

the input will follow the following format (from left to right): see [[screener columns]] for detailed rationale and application

sometimes, the user input will include that he is looking to enter [[shorts]] or to that effect. then reading through [[shorts]] is essential

---

### add this to user prompt:

"rely heavily on the knowledge base within Trading folder

identify if this is a surf trade, turf trade, rebirth trade based on the technical data

classify the individual stocks into type of trades first: surf, turf or rebirth

then provide a rank for each of the individual stock [[setup]] based on [[five star setups]] out of 5 points, atomized to 0.5 significant figure, ie. scores can end with 0 or .5 from a range of 0 to 5 points

if the market is not explicitly mentioned in the prompt, then it is assumed that the user has filtered out yellow choppy markets based on the [[market trend model]], hence unless otherwise stated, assume inputs to be in green markets and do not have to be mentioned in analysis. when asked for shorts candidates, assume a red downtrend model.

display the result with the best performing stock at the top and the worse below.

justify the decisions.

for stocks that have a score of 4 or higher, provide an open price point, [[stop loss]] price, quantity. quantity should be calculated with the following risk of drawdown equity.
- risk 0.3% of equity for 5 star setup
- risk 0.25% for 4.5 star setup
- risk 0.20% for 4 star setup
be careful while making quantity calculations with the currencies. the risk drawdown equity is in US Dollars, while the prices quoted for:

- Japanese stocks is JPY:  1 USD = 158 JPY
- Korean stocks is KRW: 1 USD = 1467 KRW
- Taiwan stocks is TWD: 1 USD = 31.6 TWD

note that Japanese stocks quantity are fixed to multiples of 100

Taiwan stocks and Japanese stocks both have 4 digit tickers, so work has to be done to sort them out using the price column currency unit (JPY, KRW, TWD)

Use these exchange rates above to calculate risk of drawdown equity in USD. the last reported equity is US$1.3m

include total size of position (quantity multiply by order price)

the stock technical and fundamental information provided have already passed through the [[surf and turf v3.3]] filters

technical analysis weighs heavier than fundamental balance sheet indicators. for example, it is twice more important to give an entry signal of tightness in recent price action ([[inside day]] for example), than any other metric used, such as the prior [[eps & sales]] results

all stocks data provided already passes [[relative strength]] filters, so entry signals should be weighed slightly heavier biased towards [[pullback]] with minimal [[atr% multiple from 50ma]], as well as more atomically, tight candles for entry days such as [[inside day]]

---



Also add with justification, if the opening order should be a:
- IBKR midprice order - An IBKR MidPrice order is designed to split the difference between the bid and ask prices to execute at the current midpoint of the NBBO (National Best Bid and Offer) or better. IBKR midprice orders are not available for Korean stocks (Korean stocks have 6 digit tickers)
- [[opening range breakout]] order - check the time of the screenshot to see if this is relevant. user is in Korean Standard Time (GMT+9)
-  market order - usually reserved for orders at the start of the day for a 4.5 stars and above setup for surf trades
- limit order - user does not prefer this order unless placing a limit order allows the [[stop loss]] to avoid being a [[wide stops]] by being able to enter closer to [[bjorgum key levels]] or [[key moving averages]] and hence have a smaller stop distance and a much bigger [[position size]] 

there is no need to justify anything for setups with a score of less than 4. merely list them out, grouped with their corresponding ranking for setups with a score less than 4

remember to limit to [[two trades a day per market]], aka two trades a day in each currency

be sure to [[stack tailwinds]] to maximize the chances of a good outcome. do this by first checking [[industry (previously known as sectors)]] to sort for the best performing sectors to be in

if there's a yellow flag, then there is an open position. in the event of an open position, analyze to see if there is an opportunity to [[add position]] or an opportunity to it [[sell into weakness]] or [[sell into strength]]. Sound the alarm if a [[breakdown]] has occurred

never change anything in the TRADING folder knowledge base without explicit permission or explicit user prompting

context can be found through the knowledge base of this TRADING folder

"Trading folder" and "knowledge base" are interchangeable terminology both referring to this trading folder where the markdown files are located

---
#instructions #setup #stock-selection #five-star-setups #entry #instructions 