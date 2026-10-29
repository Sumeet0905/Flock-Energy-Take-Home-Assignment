# Flock Energy Take-Home Assignment: NILM & Appliance Disaggregation

## Project Overview

This project implements an end-to-end data engineering, exploratory data analysis (EDA), and Non-Intrusive Load Monitoring (NILM) pipeline using the official REFIT Electrical Load Measurements dataset.

The objective is to clean household electricity readings, investigate washing-machine usage patterns, estimate appliance-level power consumption from whole-house aggregate measurements, and evaluate the performance of an appliance-disaggregation model.

The project is developed and executed in **Google Colab**, with Google Drive used for dataset storage.

### What Was Built

The solution consists of four Jupyter notebooks:

1. **Data Cleaning:** Inspects raw House 1 readings, handles timestamps and duplicates, resamples data to one-minute intervals, and investigates missing values and outliers.
2. **Washing-Machine EDA:** Analyzes washing-machine activity across four selected households, detects candidate operating cycles, and compares usage patterns.
3. **NILM Model:** Trains a Random Forest regression model, compares it against an always-off baseline, and evaluates predictions on a held-out household.
4. **Practical Implications:** Discusses potential utility applications, energy-saving opportunities, model limitations, and future improvements.

## Technology Stack

- Google Colab for notebook execution.
- Google Drive for data storage.
- Python for data processing and modeling.
- pandas and NumPy for data manipulation.
- Matplotlib and Seaborn for visualization.
- scikit-learn for Random Forest regression and evaluation.
- py7zr for extracting compressed REFIT archives.

## Dataset

The project uses the official REFIT Electrical Load Measurements dataset from the University of Strathclyde.

- [Raw REFIT data](https://pureportal.strath.ac.uk/files/62090177/REFIT_RAW_081116.7z)
- [Cleaned REFIT data](https://pureportal.strath.ac.uk/files/62090184/CLEAN_REFIT_081116.7z)
- [Official cleaned-data readme](https://pureportal.strath.ac.uk/ws/portalfiles/portal/62090183/CLEAN_READ_ME_081116.txt)
- [Dataset documentation](https://pureportal.strath.ac.uk/en/datasets/refit-electrical-load-measurements/)

The raw dataset is used for the House 1 cleaning task. The official cleaned dataset is used for washing-machine EDA and model development.

## Setup and Run Instructions

### 1. Open Google Colab

Open [Google Colab](https://colab.research.google.com/) and upload or open the four project notebooks.

### 2. Install dependencies

Run the following cell where required:

```python
!pip install -q py7zr pandas numpy matplotlib seaborn scikit-learn
```

### 3. Mount Google Drive

```python
from google.colab import drive

drive.mount("/content/drive")
```

### 4. Configure dataset paths

Place the cleaned archive in Google Drive and update the path if necessary.

```python
from pathlib import Path

DATA_DIR = Path(
    "/content/drive/MyDrive/"
    "Flock Energy - Take Home Assignment "
)

CLEAN_ARCHIVE = DATA_DIR / "CLEAN_REFIT_081116.7z"

assert CLEAN_ARCHIVE.exists(), (
    f"Archive not found: {CLEAN_ARCHIVE}"
)
```

Download the raw archive separately for the raw-data cleaning task.

### 5. Extract the cleaned dataset

```python
import py7zr

EXTRACT_DIR = DATA_DIR / "cleaned"
EXTRACT_DIR.mkdir(parents=True, exist_ok=True)

with py7zr.SevenZipFile(
    str(CLEAN_ARCHIVE), mode="r"
) as archive:
    archive.extractall(path=str(EXTRACT_DIR))

print("Extraction completed:", EXTRACT_DIR)
```

If the notebooks already contain an extraction function, reuse that implementation instead of extracting the same archive repeatedly.

### 6. Execute notebooks sequentially

Run the notebooks in this order:

1. `Notebook_1_Raw_data_cleaning.ipynb`
2. `Notebook_2_Exploratory_analysis_of_washing_machine_use.ipynb`
3. `Notebook_3_Build and evaluate an appliance-disaggregation model .ipynb`
4. `Notebook_4_Practical_Implications,Limitations&Recommendations.ipynb`

Check the output of each notebook before proceeding to the next stage.

## Assumptions

- The selected washing-machine channels are House 1: Appliance1, House 2: Appliance2, House 3: Appliance6, and House 5: Appliance1. These assignments must be verified against official REFIT metadata.
- The analysis uses one-minute power readings.
- Power above 20 W is treated as potentially active washing-machine operation.
- A candidate cycle requires at least 15 active minutes, with pauses of up to five minutes potentially bridged.
- The model uses aggregate household power and engineered temporal features to estimate washing-machine power.
- The always-off baseline predicts zero washing-machine power at every time step.

## Preprocessing Decisions

- Parse timestamps and sort readings chronologically.
- Remove duplicate timestamps using a documented rule.
- Resample power readings to one-minute intervals.
- Linearly interpolate short gaps of up to 15 minutes.
- Flag gaps longer than 15 minutes and exclude affected periods from model training.
- Investigate negative readings and extreme values before deciding whether they represent data errors or genuine observations.
- Preserve a record of excluded periods and preprocessing decisions.

Timestamp interpretation, power units, and gap-duration calculations must be consistent with the official dataset documentation.

## Validation Design

The intended validation design is a cross-household split.

- Training households: Houses 1, 2, and 3.
- Test household: House 5.
- Training data may be subsampled to 30% to reduce memory consumption in Google Colab.
- The test household must remain excluded from model fitting and target-dependent preprocessing.

This approach evaluates transfer to an unseen household and avoids the leakage associated with randomly mixing adjacent time steps from the same household.

## Evaluation Metrics

- **MAE:** Measures average absolute power-prediction error.
- **RMSE:** Penalizes larger prediction errors.
- **On/Off F1-score:** Evaluates appliance operating-state detection using the specified power threshold.

The Random Forest must be compared against the always-off baseline. Actual metric values should be reported in `RESULTS.md` after execution.

## Trade-Offs

A Random Forest was selected as a practical starting point because it captures nonlinear relationships without the complexity of deep sequence models.

Subsampling reduces memory usage but can omit rare appliance behaviors. Cross-household validation provides a more realistic generalization test than a random time-step split, but the results from one test household are not sufficient to establish universal performance.

## Possible Improvements

- Evaluate Sequence-to-Point and LSTM-based models.
- Extend disaggregation to multiple appliances.
- Add more temporal features and investigate frequency-domain features where appropriate.
- Evaluate on additional unseen households.
- Quantify prediction uncertainty.
- Add automated tests, experiment tracking, and model versioning.
- Validate performance on representative Indian household data.

## Deliverables

- Four executable Google Colab notebooks.
- `README.md`
- `DATA.md`
- `RESULTS.md`
- `Recommendation.md`

The repository should contain the completed documentation and relevant figures and metrics, without unnecessarily committing large dataset archives.

## Conclusion

This project provides a reproducible workflow for REFIT data cleaning, washing-machine usage analysis, and appliance disaggregation. Its primary objective is to demonstrate sound data handling, explainable modeling, leakage-aware validation, and practical utility-facing insights.
