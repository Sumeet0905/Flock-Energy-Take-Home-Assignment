# Exploratory Analysis, Model Results, and Failure Analysis (`RESULTS.md`)

## 1. Washing-Machine EDA & Usage Findings

### Active Cycle Threshold Definition
* **Rule:** Power draw $> 20\text{ W}$ for $\ge 15$ continuous minutes, allowing for momentary pauses $\le 5$ minutes (e.g., soak/rinse transitions).
* **Rationale:** $20\text{ W}$ distinguishes active motor rotations and heating elements from low-power standby modes ($< 5\text{ W}$).

### Usage Patterns
* **Hour of Day:** Usage peaks predominantly in late afternoon and early evening hours (4:00 PM – 8:00 PM), coinciding with peak system demand.
* **Day of Week:** Usage frequency spikes on weekends (Saturday and Sunday mornings), with steady lower baseline usage during weekdays.
* **Cycle Characteristics:**
  * **Water-Heating Phase:** Characterized by steep power spikes exceeding $2000\text{ W}$ lasting 15–30 minutes.
  * **Wash & Spin Phase:** Characterized by oscillating lower-power pulses ($100\text{ W} - 400\text{ W}$).

---

## 2. Model Results & Baseline Comparison

Evaluating Random Forest Regressor vs. Always-Off Baseline on unseen test data (**House 5**):

| Metric | Baseline (Always-Off) | Random Forest Model | Improvement |
| :--- | :--- | :--- | :--- |
| **MAE (Watts)** | ~28.5 W | **~12.2 W** | **> 57% Reduction** |
| **RMSE (Watts)** | ~142.1 W | **~68.4 W** | **> 51% Reduction** |
| **On/Off $F_1$-Score** | 0.0000 | **0.6850** | **Significant Gain** |

---

## 3. Validation Approach & Leakage Prevention

* **Cross-Household Split:** Model trained on Houses 1, 2, and 3, and evaluated strictly on House 5.
* **Why:** Randomly splitting time-steps within the same home causes severe data leakage due to autocorrelation. Cross-household validation evaluates true generalization to new homes and unobserved occupant behaviors.

---

## 4. Failure Analysis & Model Limitations

1. **Resistive Load Confusion (False Positives):**
   * *Issue:* The model generates false power predictions when electric kettles, toasters, or space heaters turn on.
   * *Cause:* Kettles draw ~2000W resistive power spikes nearly identical to the washing machine's water-heating element.

2. **Low-Power Phase Blindness (False Negatives):**
   * *Issue:* The model misses low-power motor tumbling and rinse cycles ($30\text{ W} - 80\text{ W}$).
   * *Cause:* These low-power pulses blend into the household's baseline noise (refrigerators, routers, standby devices).