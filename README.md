# Temperature-Dependent Nonlinear Calibration of Glass pH Electrodes for Negative pH Applications

**Sarawud Saleesongsom, Dominik Weiss, and Yves Plancherel**
*ACS Omega* **2026**, *11* (27), 40120–40133 · DOI: [10.1021/acsomega.6c01903](https://doi.org/10.1021/acsomega.6c01903)

---

## Overview

Combined glass pH electrodes follow a linear Nernstian response over the conventional
range (roughly pH 1–12), but in the strong-acid / acid-error region the response
becomes **non-Nernstian** and curves away from a straight line. This repository provides
ready-to-run Jupyter notebooks that calibrate a glass electrode across both regimes,
including a new method for reliable measurement of **negative pH (down to pH −5)** and a
**temperature-dependent** correction that lets a single calibration be applied at any
temperature.

Two response models underpin the workflows:

- **Linear Nernst** - for the standard range: `EMF = E0 − S·pH`.
- **Nonlinear logistic** - for very low / negative pH: `EMF = A / (1 + exp(k·(pH − x0)))`,
  invertible to recover pH from a measured EMF.

Temperature dependence is captured by fitting each model's parameters against
temperature: the Nernst slope `S(T)` and standard potential `E0(T)` by linear
regression, and the logistic parameters `A(T)`, `k(T)`, `x0(T)` by polynomials whose
degree is chosen automatically using the small-sample-corrected Akaike criterion
(**AICc**).

<img width="4601" height="4299" alt="Github graphic" src="https://github.com/user-attachments/assets/3e387d1b-2a37-416c-a828-17c44bc881b2" />


## The four calibration workflows

The graphic above summarises the four paths the notebooks cover:

| Workflow | Notebook · Option | Range | Model |
|---|---|---|---|
| Standard pH at a **specific** temperature | `standard_pH_calibration.ipynb` · Option 1 | pH 1–12 | Linear Nernst |
| Standard pH at **any** temperature | `standard_pH_calibration.ipynb` · Option 2 | pH 1–12 | Nernst + `S(T)`, `E0(T)` |
| Negative pH at a **specific** temperature | `Negative_pH_calibration_Tcorrection.ipynb` · Option 1 | down to pH −5 | Nonlinear logistic |
| Negative pH at **any** temperature | `Negative_pH_calibration_Tcorrection.ipynb` · Option 2 | 5–60 °C, down to pH −5 | Logistic + `A(T)`, `k(T)`, `x0(T)` (AICc) |

## Repository contents

```
.
├── README.md
├── Diagram/
│   └── Github graphic.png            # calibration workflow (graphical abstract)
└── Notebooks/
    ├── standard_pH_calibration.ipynb            # standard range (pH 1–12)
    ├── Negative_pH_calibration_Tcorrection.ipynb# very low / negative pH (down to −5)
    └── calibration_datasets.xlsx                # example data (edit to use your own)
```

## Getting started

Requirements: **Python 3.9+** with

```bash
pip install numpy scipy pandas matplotlib openpyxl jupyter
```

Then launch either notebook:

```bash
cd Notebooks
jupyter notebook   # or jupyter lab
```

Each notebook runs top-to-bottom and is organised into two **Options**. Enter your own
measurements either in the inline arrays (single-temperature workflows) or in
`calibration_datasets.xlsx` (multi-temperature workflows), then re-run the cells.

## Using the notebooks

### `standard_pH_calibration.ipynb`

- **Option 1 - pH at a specific temperature.** Enter a standard 3-point buffer
  calibration (pH 4, 7, 10) measured at one temperature; the notebook fits the linear
  Nernst response and converts sample EMF to pH.
- **Option 2 - pH at any temperature.** Load 3-point calibrations measured at several
  temperatures. The notebook (Step 1) plots the raw data, (Step 2) fits a Nernst line at
  each temperature, (Step 3) fits `S(T)` and `E0(T)` by linear regression with 95%
  confidence bands, (Step 4) builds the full `EMF(pH, T)` model, and (Step 5) predicts
  sample pH at any temperature in range.

### `Negative_pH_calibration_Tcorrection.ipynb`

- **Option 1 - negative pH at a specific temperature.** Combine NIST/DIN buffers
  (pH 1.68, 3, 4) with an H₂SO₄ standard series reaching down to pH −5, then fit a single
  logistic curve and invert it to recover pH from EMF.
- **Option 2 - negative pH at any temperature (5–60 °C).** Fit a logistic to every
  replicate run, make `A`, `k`, `x0` temperature-dependent using AICc-selected
  polynomials (with 95% confidence bands), build the full temperature-dependent
  calibration, and predict sample pH at any temperature in range.

> **Important:** before running a negative-pH calibration, recondition the glass
> electrode as described in the paper. Skipping reconditioning degrades the response in
> the strong-acid region.

## Data format

`calibration_datasets.xlsx` holds the example data (this study's real measurements) and
is the only file you need to edit for the multi-temperature workflows.

**Sheet `Standard`** - one row per buffer measurement:

| Column | Meaning |
|---|---|
| `Temperature_C` | temperature of the calibration (°C) |
| `pH` | certified buffer pH |
| `EMF_mV` | measured EMF (mV) |

**Sheet `Negative`** - one row per measurement across both standard types:

| Column | Meaning |
|---|---|
| `Temp_set_C` | nominal set temperature of the run (°C) |
| `Replicate` | replicate index for that temperature |
| `pH` | standard pH (buffer or H₂SO₄) |
| `EMF_mV` | measured EMF (mV) |
| `Standard` | `buffer` or `H2SO4` |
| `Temp_measured_C` | actual measured temperature (°C) |

Replace the example rows with your own data, keeping the same column names.

## Method summary

1. **Standard range.** A Nernst line `EMF = E0 − S·pH` is fit to the buffers. Across
   temperature, `S(T) = Ks·(T + 273.15) + C` and `E0(T) = E0,25 + (dE0/dT)·(T − 25)` are
   obtained by linear regression.
2. **Negative range.** A logistic `EMF = A / (1 + exp(k·(pH − x0)))` describes the
   non-Nernstian roll-off. The logistic is fit to each replicate run, giving one
   `(T, A, k, x0)` estimate per run.
3. **Temperature model.** `A(T)`, `k(T)` and `x0(T)` are each fit by a polynomial in
   temperature; the degree is selected by AICc (lowest score wins), and the full
   calibration `EMF(pH, T)` is assembled from the three parameter functions.
4. **Prediction.** Inverting the fitted model converts a measured `(EMF, T)` pair into
   pH, valid across the calibrated pH and temperature range.

## Citation

If you use this code or method, please cite:

> Saleesongsom, S.; Weiss, D.; Plancherel, Y. Temperature-Dependent Nonlinear
> Calibration of Glass pH Electrodes for Negative pH Applications. *ACS Omega* **2026**,
> *11* (27), 40120–40133. DOI: 10.1021/acsomega.6c01903.
