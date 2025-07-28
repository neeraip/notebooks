# 📘 README – Bayesian RTK Calibration for EPA SWMM (PySWMM)

This guide explains how to run a full **Bayesian Optimization of RTK Unit Hydrograph parameters** for an EPA SWMM model using `pyswmm` and `skopt`, in a **Jupyter notebook** on a **Linux VM**.

---

## 📂 Folder Structure

```
project_folder/
├── Base_INP_File/
│   └── Example 1.inp
├── Optimized_INP_Files/Datetime Stamp Folder (YYYY-MM-DD_HH-MM-SS)
│   └── [generated optimized .inp files]
├── notebook.ipynb
└── README.md
```

---

## ✅ Step-by-Step Instructions

### 1. Install Required Packages
```bash
pip install pyswmm scikit-optimize tqdm tabulate
```

### 2. Prepare Your `.inp` File
- Place your **calibration model** inside `Base_INP_File/`
- Confirm that the `[HYDROGRAPHS]` section includes 3 entries (Short, Medium, Long) for a specific link.
- Make sure the `[TIMESERIES]` section contains **Flow(cfs)** time series under a label (e.g., `Flow(cfs)`)

### 3. Configure Simulation Parameters in the Notebook
Modify the following variables near the top of the notebook:
```python
START_DATE = "07/16/2024"
START_TIME = "00:00:00"
END_DATE = "07/18/2024"
END_TIME = "00:00:00"
ROUTING_STEP = "00:05:00"
CALIBRATION_LINK_ID = "WDS-103_WDS-290"
```

### 4. Run the Notebook
- Run the single cell code.
- It will:
  - Update simulation dates inside the INP file.
  - Parse the observed flow series.
  - Set up a 9D search space for R, T, K.
  - Run up to `MAX_CALLS` simulations using `gp_minimize`
  - Stop early if `TARGET_NSE` is reached.

---

## 🧠 Technical Explanation

### 🔁 Unit Hydrograph (RTK) in EPA SWMM
- RTK represents **rain-derived I&I** (RDII) inflows.
- Each hydrograph has 3 parts: **Short, Medium, Long response**
- Each part uses:
  - **R** = fraction of rainfall entering this pathway
  - **T** = time to peak (hours)
  - **K** = ratio of recession limb to T

3 hydrographs x (R, T, K) = 9 parameters to calibrate.

### ⚠️ Constraints
- R1 + R2 + R3 <= 1
- T1 < T2 < T3
- K1 < K2 < K3
- NSE is used as objective metric

Instead of independently optimizing R1, R2, and R3 — which can easily violate the rule:

R1 + R2 + R3 ≤ 1.0

We define:
R_total ∈ [0.01, 0.9]          # Total R volume (how much rainfall turns to flow)
alpha1 ∈ [0, 1]                # % of R_total for short-term response
alpha2 ∈ [0, 1 - alpha1]       # % of R_total for medium-term
alpha3 = 1 - alpha1 - alpha2   # Remaining goes to long-term

R1 = alpha1 * R_total
R2 = alpha2 * R_total
R3 = (1 - alpha1 - alpha2) * R_total

📊 Example:
Let’s say the optimizer picks:

R_total = 0.6

alpha1 = 0.5

alpha2 = 0.3

Then:

R1 = 0.5 × 0.6 = 0.30

R2 = 0.3 × 0.6 = 0.18

R3 = 0.2 × 0.6 = 0.12

So:

✅ R1 + R2 + R3 = 0.60 (matches R_total)

✅ Total response remains valid

✅ Each component is automatically normalized
---

## 🔧 Key Parameters You Can Change

| Variable           | Purpose                                      |
|--------------------|-----------------------------------------------|
| `BASE_INP_PATH`    | Input SWMM model to optimize                 |
| `CALIBRATION_LINK_ID` | Target link to compare flow                |
| `START_DATE/TIME`  | Start of simulation                          |
| `END_DATE/TIME`    | End of simulation                            |
| `ROUTING_STEP`     | Model time step (e.g., 5-min)                |
| `MAX_CALLS`        | Maximum number of iterations (default 600)  |
| `INITIAL_POINTS`   | Initial random samples before optimization  |
| `TARGET_NSE`       | Early stopping threshold for best-fit       |

---

## 📈 Outputs Generated

- Optimized `.inp` files in `Optimized_INP_Files/Datetime Stamp Folder (YYYY-MM-DD_HH-MM-SS)`
- NSE score for each simulation
- Best simulation: time series plot (Observed vs Simulated)
- RTK Matrix for best-fit parameters printed as a table
- Runtime printout with elapsed time and progress
- Summary Table with Comparison of Peak Flow (cfs) and Total Volume (ft3) between Observed and Simulated Flow for the Simulation Duration.
---

## ✅ Final Notes
- This notebook uses `pyswmm` for in-notebook simulation — no need to run SWMM externally.
- Bayesian Optimization is efficient for this 9-variable search space.
- Supports early stopping if good NSE is reached early.

---

## 📬 Contact / Troubleshooting
- Ensure `pyswmm` is installed correctly and SWMM DLLs are available
- Confirm your INP has correct `[HYDROGRAPHS]` and `[TIMESERIES]` sections
- If simulation fails silently, check formatting and units

---

Enjoy fast, automated RTK calibration in your own SWMM models! 🚀
