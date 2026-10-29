# Raw Data Coverage and Quality Findings (`DATA.md`)

## 1. Raw-Data Coverage & Quality Findings

### Dataset Profile
* **Source:** Official REFIT Electrical Load Measurements Dataset (Cleaned 1-minute version).
* **Scope:** Long-term continuous smart-meter monitoring across UK households, capturing whole-home aggregate active power alongside individual sub-metered appliance channels.
* **Target Households Analyzed:** House 1, House 2, House 3, and House 5.

### Identified Quality Issues
1. **Timestamp Irregularities & Duplicates:** Raw data logs contained duplicate minute readings and non-uniform sampling intervals due to sensor transmission delays.
2. **Data Gaps & Communication Drops:** Intermittent missing records ranging from short 1–3 minute dropouts to multi-day missing intervals.
3. **Zero-Power Flatlines:** Extended zero-reading periods caused by sensor disconnection or prolonged occupant absence.

---

## 2. Data Cleaning Steps & Decisions

* **Step 1: Chronological Ordering & Indexing**
  * Converted time strings into a standardized UTC `DatetimeIndex`.
  * Removed exact duplicate timestamps, retaining the first recorded instance.

* **Step 2: Gap Categorization and Imputation Rules**
  * **Short Gaps ($\le 15\text{ minutes}$):** Imputed using forward-fill and linear interpolation. This retains the temporal continuity required for rolling statistics feature engineering without introducing artificial volatility.
  * **Long Gaps ($> 15\text{ minutes}$):** Explicitly categorized and flagged as `Is_Outage = True`. These periods were completely excluded from model training to prevent the model from learning fake zero-consumption regimes during grid or sensor outages.

* **Step 3: Appliance Mapping Standardization**
  * Mapped individual appliance channels explicitly using REFIT metadata:
    * `House 1`: `Appliance1` (Washing Machine)
    * `House 2`: `Appliance2` (Washing Machine)
    * `House 3`: `Appliance6` (Washing Machine / Tumble Dryer)
    * `House 5`: `Appliance1` (Washing Machine)

---

## 3. Remaining Concerns & Limitations

* **Unmetered Background Loads:** Non-submetered loads (e.g., lighting, electronics, standby power) add baseline noise to the aggregate load signal, making low-power washing machine phases harder to disaggregate.
* **Anomalous Voltage Spikes:** Transient grid surges occasionally appear in aggregate load logs, which could mimic appliance startup spikes.