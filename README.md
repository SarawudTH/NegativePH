# NegativePH

# Temperature-Dependent Nonlinear Calibration of Glass pH Electrodes for Negative pH Applications

Data and analysis code for the paper:

**Temperature-Dependent Nonlinear Calibration of Glass pH Electrodes for Negative pH Applications**

Sarawud Saleesongsom, Dominik Weiss, and Yves Plancherel

*ACS Omega* DOI: [10.1021/acsomega.6c01903](https://doi.org/10.1021/acsomega.6c01903)

This repository contains the raw calibration data, the analysis code (MATLAB and Python), and the derived coefficient tables that underpin the temperature-dependent nonlinear (logistic) calibration model reported in the paper.

## Overview

Conventional linear calibration of glass pH electrodes with standard NIST/DIN buffers breaks down at high proton activity. This work develops and validates a nonlinear, temperature-corrected calibration protocol that extends reliable glass-electrode measurements down to pH = −5, over the temperature range 5–60 °C, using NIST buffers (pH 1.68–10.01) and sulfuric acid standards (0.16 mmol·L⁻¹ to 9.69 mol·L⁻¹ H₂SO₄). Reference proton activities are computed with the Pitzer model and the MacInnes assumption in PHREEQC.

The negative-pH response at each temperature is described by a logistic function:

```
EMF(pH) = A / (1 + exp(-k * (pH - x0)))
```

and the temperature dependence of the logistic parameters A, k, and x0 is captured by polynomial fits A(T), k(T), and x0(T) with Monte Carlo uncertainty propagation.

## Repository contents

### Raw calibration data
- **`StandardpHcalibration_T5_65.xlsx`** — Standard (NIST buffer) pH calibration measurements, 5–65 °C. Source data for the Nernstian E0(T) and slope S(T) fits.
- **`NegativepHCalibration_T5_60.xlsx`** — Negative-pH (H₂SO₄ standard) calibration measurements, 5–60 °C. The `EMF-pH Data` sheet holds the per-run raw data: each row has a run ID (e.g. `25C_1_0.0001` = 25 °C, replicate 1) with Temperature (°C), pH, and EMF (mV). Per-temperature summary sheets (`5C`, `15C`, `25C`, `40C`, `50C`) give the replicate-averaged values.
- **`pHCalculationValidationPrimatrode_Analysis.xlsx`** — Independent validation: pH recovered from EMF using the calibration model (Metrohm Primatrode), compared against reference pH.

### Analysis code

**MATLAB**
- **`NegativeCalibrationDiffTemp.m`** — Fits the logistic function to each negative-pH calibration run and exports the per-run A, k, x0 with 95% bounds and standard errors → `NegativeCalibration_Logistic_Stats.csv`.
- **`TempVSLogisticCoefficients.m`** — Fits the temperature dependence of the logistic parameters with a Monte Carlo (5000-iteration) polynomial regression: A(T) and k(T) degree 3, x0(T) degree 2 → the three `MC_poly_*_summary.csv` files.

**Python** (equivalents of the two MATLAB scripts, same base names)
- **`NegativeCalibrationDiffTemp.py`** — Same per-run logistic fit. Reads the raw per-run data from the `EMF-pH Data` sheet (grouping rows by run ID; falls back to per-run sheets if present) → `NegativeCalibration_Logistic_Stats.csv`.
- **`TempVSLogisticCoefficients.py`** — Same Monte Carlo polynomial fit of A(T), k(T), x0(T) → the three `MC_poly_*_summary.csv` files.

### Derived results
- **`NegativeCalibration_Logistic_Stats.csv`** — Per-run logistic fit parameters (A, k, x0) with uncertainties, one row per calibration run.
- **`MC_poly_A_deg3_summary.csv`** — Coefficients of A(T) (degree 3).
- **`MC_poly_k_deg3_summary.csv`** — Coefficients of k(T) (degree 3).
- **`MC_poly_x0_deg2_summary.csv`** — Coefficients of x0(T) (degree 2).

These three summary files are the source of the temperature-dependence coefficients reported in **Table 1** of the article and **Table S32** of the Supporting Information.

## Analysis pipeline

```
NegativepHCalibration_T5_60.xlsx  (EMF-pH Data sheet: raw EMF vs. pH per run)
            │
            ▼   NegativeCalibrationDiffTemp.m   /   NegativeCalibrationDiffTemp.py
            │       (logistic fit per run)
            │
NegativeCalibration_Logistic_Stats.csv          (per-run A, k, x0)
            │
            ▼   TempVSLogisticCoefficients.m   /   TempVSLogisticCoefficients.py
            │       (Monte Carlo polynomial fit vs. T)
            │
MC_poly_A_deg3 / MC_poly_k_deg3 / MC_poly_x0_deg2 _summary.csv
                                                (A(T), k(T), x0(T) coefficients
                                                 → Table 1 / Table S32)
```

## Requirements

**MATLAB** — Curve Fitting Toolbox recommended for the logistic fits.

**Python 3** — install dependencies with:

```bash
pip install numpy pandas scipy matplotlib openpyxl
```

Run from the folder that contains the data files:

```bash
python NegativeCalibrationDiffTemp.py      # -> NegativeCalibration_Logistic_Stats.csv
python TempVSLogisticCoefficients.py       # -> MC_poly_*_summary.csv
```

## Citation

If you use these data or code, please cite:

```
Saleesongsom, S.; Weiss, D.; Plancherel, Y.
Temperature-Dependent Nonlinear Calibration of Glass pH Electrodes for Negative pH Applications.
ACS Omega. https://doi.org/10.1021/acsomega.6c01903
```
