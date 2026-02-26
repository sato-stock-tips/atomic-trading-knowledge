### How is Relative Volume at Time calculated

**Relative Volume at Time** only takes the average volume of a single bar per day (for the last 10 days), whichever bar corresponds to the current time. Screener calculate Relative Volume at Time only for 5 minutes bars. For example, we're calculating Relative Volume at Time on 5 minutes (5m) time frame 17 Oct `22 at 10:30. It takes the average volume of each 10:30 5 minutes bar for the last 10 days and calculates the relative volume based on that data. Bars (candles) and volumes are shown in the table:

|                                                      |          |
| ---------------------------------------------------- | -------- |
| Bars taken for calculating Relative Volume at a time | Volume   |
| 03 Oct `22 10:30                                     | 854.093K |
| 04 Oct `22 10:30                                     | 1.001M   |
| 05 Oct `22 10:30                                     | 1.321M   |
| 06 Oct `22 10:30                                     | 623.869K |
| 07 Oct `22 10:30                                     | 1.004M   |
| 10 Oct `22 10:30                                     | 931.324K |
| 11 Oct `22 10:30                                     | 1.31M    |
| 12 Oct `22 10:30                                     | 752.673K |
| 13 Oct `22 10:30                                     | 782.339K |
| 14 Oct `22 10:30                                     | 1.032M   |

---
## Why Relative Volume at Time is Superior

### 1. **Accounts for Intraday Patterns**

Relative Volume at Time compares current volume to the _typical volume at that specific time of day_ over the past 10 days. This is crucial because:

- Market volume naturally varies throughout the trading day (higher at open/close, lower mid-day)
- A volume spike at 10:30 AM should be compared to typical 10:30 AM volume, not to an average that includes after-hours periods
- Standard Relative Volume would incorrectly flag normal opening volume as "high" if compared against lower afternoon averages

### 2. **More Accurate Anomaly Detection**

When scanning for unusual activity (breakouts, institutional accumulation, etc.), Relative Volume at Time:

- Eliminates false signals from normal intraday volume patterns
- Highlights true deviations: "This 10:30 bar has 3x the volume of typical 10:30 bars"
- Provides context-aware signals that standard Relative Volume misses

### 3. **Better for Intraday Trading**

If your Trading folder focuses on intraday strategies (which most volume-based strategies do):

- Relative Volume at Time gives apples-to-apples comparisons
- More actionable for entries/exits during specific market hours
- Aligns with how professional traders think about [[volume]]