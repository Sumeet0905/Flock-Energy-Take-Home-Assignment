# Utility-Facing Summary, Limitations, and Recommendations (`Recommendation.md`)

## 1. Practical Energy-Saving & Customer Opportunity

### Recommendation: Automated Off-Peak Wash Incentive Program
* **Opportunity:** EDA demonstrates that washing machines are frequently run during evening peak tariff windows (4:00 PM – 8:00 PM), driving up consumer energy bills and utility peak demand. Since water heating draws up to $2000\text{ W}+$, shifting cycles to off-peak hours (e.g., after 10:00 PM) offers immediate bill reductions.
* **How to Measure Success (A/B Testing Framework):**
  * *Control Group:* Receives standard monthly billing statement.
  * *Treatment Group:* Receives automated mobile app alerts when a peak-hour wash cycle is detected ("Shift your wash to 10 PM to save ₹XX/month").
  * *Metric:* Track the percentage shift in weekly kWh consumed by washing machines from peak to off-peak hours over 90 days.

---

## 2. Key Limitations & UK-to-India Applicability

* **Overlapping Resistive Signatures:** High risk of false positives from appliances with similar resistive heating profiles (e.g., kettles, irons, space heaters).
* **Missing Data & Sensor Gaps:** Communication drops distort automated cycle counting and energy calculations if not properly flagged.
* **Applying UK Results (REFIT) to India:**
  * *Appliance Heating Mechanics:* UK washing machines in REFIT rely heavily on internal electric heating elements to heat cold water (40°C–60°C), creating massive $2000\text{ W}+$ spikes. In contrast, Indian households predominantly use top-load washing machines connected directly to ambient water or solar heaters, emitting much lower power profiles ($200\text{ W} - 500\text{ W}$).
  * *Baseline Grid Demand:* Indian household power profiles are heavily dominated by cooling loads (ceiling fans, air conditioners, air coolers), requiring complete recalibration of feature extraction models.

---

## 3. Impact of Coarser Data Granularity (15-min / 30-min Smart Meters)

If only 15-minute or 30-minute aggregate smart-meter data were available, appliance disaggregation would become drastically harder:
1. **Loss of High-Frequency Signatures:** Transient 2–3 minute heating spikes and short motor rotation pulses are smoothed out and averaged away across a 30-minute window.
2. **Feature Blending:** A washing machine's multi-stage signature gets blended into the baseline load of refrigerators and standby electronics.
3. **Reduced Performance:** Cycle boundary detection, peak estimation, and On/Off $F_1$-scores degrade significantly, making real-time disaggregation infeasible without high-frequency (1-minute or sub-minute) telemetry.