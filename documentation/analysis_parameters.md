# CNC Lathe Digital Twin — Analysis Parameters & Project Guide

> A complete reference for understanding the dataset, EDA steps, ML pipeline, and Digital Twin architecture used in this project.

---

## Table of Contents
1. [What is a Digital Twin?](#1-what-is-a-digital-twin)
2. [Dataset Overview](#2-dataset-overview)
3. [Dataset Columns Explained](#3-dataset-columns-explained)
4. [What is EDA?](#4-what-is-eda-exploratory-data-analysis)
5. [EDA Steps — What Each Step Does](#5-eda-steps--what-each-step-does)
6. [ML Tasks on This Dataset](#6-ml-tasks-on-this-dataset)
7. [CAD Model — Do We Need It?](#7-cad-model--do-we-need-it)
8. [Three Levels of Digital Twin](#8-three-levels-of-digital-twin)
9. [CAD Parts of a CNC Lathe](#9-cad-parts-of-a-cnc-lathe)
10. [How CAD and ML Work Together](#10-how-cad-and-ml-work-together)
11. [Project Folder Structure](#11-project-folder-structure)

---

## 1. What is a Digital Twin?

A **Digital Twin** is a virtual replica of a physical machine that mirrors its real-time behaviour using sensor data, physics models, and machine learning.

For a CNC lathe, the Digital Twin:
- Monitors spindle speed, temperature, vibration, and cutting forces continuously
- Predicts when a tool is about to fail — before it actually fails
- Detects faults like chatter, thermal drift, and excessive wear automatically
- Allows engineers to simulate "what-if" cutting scenarios without running the real machine

**Why it matters:** A CNC lathe breaking down mid-production can cost thousands in scrap parts, machine damage, and downtime. A digital twin prevents this by giving early warnings.

---

## 2. Dataset Overview

The dataset used in this project is a **synthetic dataset** generated to simulate real CNC lathe sensor behaviour. It is used to build and validate the ML pipeline before switching to real machine data.

| Property | Value |
|----------|-------|
| Total samples | 2,000 rows |
| Total features | 21 columns |
| Sampling interval | 8–15 seconds per sample |
| Tool wear range | 0.00 mm → 0.45 mm |
| Fault classes | 4 (Normal, Tool Wear, Chatter, Thermal Drift) |

### Fault distribution

| Fault | Label | Count | % |
|-------|-------|-------|---|
| Normal | 0 | 1552 | 77.6% |
| Tool Wear | 1 | 284 | 14.2% |
| Chatter | 2 | 84 | 4.2% |
| Thermal Drift | 3 | 80 | 4.0% |

> **Note:** The dataset is imbalanced — Normal samples dominate. This must be handled during ML training using techniques like SMOTE or `class_weight='balanced'`.

### Tool condition distribution

| Condition | Wear range | Count |
|-----------|-----------|-------|
| Good | 0.00 – 0.15 mm | 850 |
| Worn | 0.15 – 0.30 mm | 866 |
| Critical | 0.30 – 0.45 mm | 284 |

---

## 3. Dataset Columns Explained

### Identification columns
| Column | Description |
|--------|-------------|
| `sample_id` | Unique row number (1–2000) |
| `timestamp_s` | Time in seconds from machine start |

### Cutting parameters (inputs you control on the lathe)
| Column | Unit | Description |
|--------|------|-------------|
| `spindle_speed_rpm` | RPM | How fast the spindle rotates |
| `feed_rate_mm_min` | mm/min | How fast the tool moves along the workpiece |
| `depth_of_cut_mm` | mm | How deep the tool cuts into the material |
| `cutting_speed_m_min` | m/min | Surface speed at the cutting point |
| `material_hardness_HRC` | Rockwell C | Hardness of the workpiece material |

### Electrical signals (from the machine's motor)
| Column | Unit | Description |
|--------|------|-------------|
| `spindle_motor_current_A` | Amperes | Power drawn by the spindle motor. Increases when tool is worn or cutting is harder. |
| `spindle_torque_Nm` | Newton-metres | Rotational force of the spindle. Rises with tool wear and harder materials. |

### Thermal readings (temperature sensors)
| Column | Unit | Description |
|--------|------|-------------|
| `spindle_temp_C` | °C | Temperature at the spindle bearing. High temp = thermal drift risk. |
| `coolant_temp_C` | °C | Temperature of the cutting coolant fluid. |
| `ambient_temp_C` | °C | Workshop air temperature. |

### Vibration signals (from accelerometers)
| Column | Unit | Description |
|--------|------|-------------|
| `vibration_x_g` | g (9.81 m/s²) | Vibration in X direction (cross-feed axis) |
| `vibration_y_g` | g | Vibration in Y direction (vertical axis) |
| `vibration_z_g` | g | Vibration in Z direction (feed axis) |

> Chatter causes vibration to spike 3–4× above normal levels. This is the primary signal for chatter detection.

### Quality & wear outputs (what you want to predict)
| Column | Unit | Description |
|--------|------|-------------|
| `surface_roughness_Ra` | μm | Surface finish quality of the machined part. Higher = rougher = worse quality. |
| `dimensional_error_mm` | mm | Deviation from target dimension. Thermal drift causes this to increase. |
| `tool_wear_mm` | mm | Actual flank wear of the cutting tool. Critical threshold = 0.35 mm. |
| `cycle_time_s` | seconds | Time to complete one machining cycle. Increases as tool wears. |

### Target columns (labels for ML)
| Column | Type | Description |
|--------|------|-------------|
| `tool_condition` | String | Good / Worn / Critical — for classification |
| `fault_label` | Integer (0–3) | 0=Normal, 1=Tool Wear, 2=Chatter, 3=Thermal Drift |

---

## 4. What is EDA? (Exploratory Data Analysis)

**EDA means looking at and understanding your data carefully before building any ML model.**

Think of it like this: before a doctor prescribes medicine, they first read your test reports, check your history, and understand your condition. EDA is the same — you study the data before making decisions.

Without EDA you risk:
- Training a model on wrong or dirty data
- Missing class imbalance that breaks model accuracy
- Using irrelevant features that add noise
- Getting high accuracy numbers that are meaningless

**EDA answers three questions:**
1. Is the data correct and complete?
2. Do the sensor signals actually carry useful information for fault detection?
3. Which features matter most for the ML model?

---

## 5. EDA Steps — What Each Step Does

### Step 1 — Import libraries
**What:** Load pandas, numpy, matplotlib, seaborn into Python.  
**Why:** These are ready-made tools. pandas reads the CSV table, numpy does math, matplotlib/seaborn draw charts. You don't write these from scratch.  
**Analogy:** Opening your toolbox before starting work.

---

### Step 2 — Load the dataset (`df.head()`)
**What:** Read the CSV file into a Python DataFrame. Show the first 5 rows.  
**Why:** Confirm the file loaded correctly, column names are right, and data looks as expected.  
**Analogy:** Opening an Excel file to check it is not corrupted.

---

### Step 3 — Basic statistics (`df.describe()`, `df.isnull()`)
**What:** Get min, max, mean, standard deviation for every column. Check for missing values.  
**Why:** Catch data problems early — values out of range (e.g. negative RPM), missing sensor readings, or columns that have no variation.  
**What to look for:**
- `spindle_speed_rpm` should be between 400–3000
- `tool_wear_mm` should be between 0.00–0.45
- All `isnull()` counts should be 0

---

### Step 4 — Fault class distribution (bar + pie chart)
**What:** Count how many samples belong to each fault class. Plot as bar and pie chart.  
**Why:** Reveals class imbalance. A model trained on 77% Normal samples will just predict "Normal" for everything and appear 77% accurate — while completely failing to detect actual faults.  
**Analogy:** If you train someone to spot defective products but show them 1000 good ones and only 5 defective — they will be useless at spotting defects.  
**Action required:** Use `class_weight='balanced'` or SMOTE oversampling before training.

---

### Step 5 — Tool condition distribution
**What:** Count Good / Worn / Critical samples.  
**Why:** If you want to build a tool condition classifier, you need enough examples of each class. Very few "Critical" samples means the model may never learn to detect it.

---

### Step 6 — Tool wear & current over time (line chart)
**What:** Plot `tool_wear_mm` and `spindle_motor_current_A` against `timestamp_s`.  
**Why:** Validates the dataset physics. On a real lathe, tool wear grows gradually over time and current rises as the tool gets duller (more force needed). If this trend does not appear — the data is wrong.  
**Expected result:** Wear line rises steadily from 0 → 0.35 mm. Current shows an upward trend with noise.  
**Analogy:** Confirming that a patient's fever actually shows up in the temperature readings.

---

### Step 7 — Sensor readings by fault type (box plots)
**What:** For 5 key sensors, draw box plots grouped by fault label.  
**Why:** If chatter does NOT cause higher vibration in the data — then vibration is a useless feature for chatter detection and the model will fail. This step confirms that each fault leaves a detectable signature in the sensors.  
**Expected observations:**

| Fault | Expected sensor signature |
|-------|--------------------------|
| Tool Wear | Higher current, higher torque, worse surface roughness |
| Chatter | Vibration 3–4× higher than Normal |
| Thermal Drift | Higher spindle temp, higher coolant temp, larger dimensional error |
| Normal | All readings within baseline range |

---

### Step 8 — Correlation heatmap
**What:** Calculate and visualise correlation between all numeric features as a colour grid.  
**Why:** Identifies redundant features. If `spindle_motor_current_A` and `spindle_torque_Nm` are 95% correlated, they carry the same information — you may only need one of them in the ML model.  
**How to read it:**
- **Red (close to +1):** Features move together — both increase or decrease together
- **Blue (close to -1):** Features move opposite — one increases when the other decreases
- **White (near 0):** No relationship

---

### Step 9 — Top features correlated with tool wear
**What:** Rank all features by their absolute correlation with `tool_wear_mm`. Plot as a horizontal bar chart.  
**Why:** Tells you which sensors are most predictive of tool wear. These become the primary input features for your ML model.  
**Expected top features:** `surface_roughness_Ra`, `spindle_motor_current_A`, `spindle_torque_Nm`, `cycle_time_s`  
**Analogy:** If you had to guess a person's age using only 3 clues — you would pick the most informative ones, not random ones. This step finds those clues for tool wear.

---

### Step 10 — Chatter vibration analysis (histogram + scatter)
**What:** Compare vibration magnitude distributions for Normal vs Chatter samples. Scatter plot of vibration vs surface roughness coloured by fault.  
**Why:** Confirms that chatter is visually separable from normal in the vibration signal. If the two distributions overlap completely — the ML model cannot learn to distinguish them.  
**Vibration magnitude formula:** `√(vx² + vy² + vz²)`  
**Expected result:** Chatter histogram is shifted clearly to the right (higher vibration) compared to Normal.

---

## 6. ML Tasks on This Dataset

Four machine learning problems can be solved using this dataset:

### Task 1 — Fault Classification (most important)
- **Input features:** All 15 sensor columns
- **Target:** `fault_label` (0/1/2/3)
- **Model:** Random Forest, XGBoost
- **Use case:** Real-time alarm system — alert the operator when a fault is detected
- **Metric:** F1-score (weighted), Confusion matrix

### Task 2 — Tool Wear Regression
- **Input features:** Current, torque, vibration, roughness, cutting parameters
- **Target:** `tool_wear_mm` (continuous value)
- **Model:** XGBoost Regressor, Random Forest Regressor
- **Use case:** Predict exact wear in mm — schedule tool replacement before failure
- **Metric:** MAE (target < 0.02 mm), R²

### Task 3 — Surface Roughness Prediction
- **Input features:** RPM, feed rate, depth of cut, vibration, tool wear
- **Target:** `surface_roughness_Ra`
- **Model:** Gradient Boosting, Neural Network
- **Use case:** Predict part quality without stopping to measure — in-process quality control
- **Metric:** MAE, R²

### Task 4 — Remaining Useful Life (RUL) Prediction
- **Input features:** Time-series window of all sensor readings
- **Target:** Cycles remaining until tool_wear_mm reaches 0.35 mm
- **Model:** LSTM, GRU
- **Use case:** Tell the operator exactly how many more parts can be made before tool must be replaced
- **Metric:** RMSE, custom RUL score

---

## 7. CAD Model — Do We Need It?

**Short answer: Not for the ML model. Yes for a full industrial Digital Twin.**

The ML model works purely on sensor data — it does not know or care what the machine looks like. CAD is needed only if you want to:
- Simulate physics (stress, heat, vibration) on the machine geometry
- Generate synthetic training data for rare faults (e.g. bearing seizure)
- Visualise the machine state in 3D in real time
- Run what-if cutting parameter simulations

---

## 8. Three Levels of Digital Twin

### Level 1 — Data-driven Digital Twin ✅ (This project)
- **Components:** Sensors + ML model + Dashboard
- **CAD needed:** No
- **What it does:** Detects faults in real time, predicts tool wear and RUL from sensor data
- **Build time:** Weeks to months
- **Used for:** Predictive maintenance, operator alerts, machine health monitoring

### Level 2 — Physics-informed Digital Twin
- **Components:** Sensors + ML + Partial CAD + FEA simulation
- **CAD needed:** Partially (spindle, tool holder, workpiece geometry)
- **What it adds:** Physics-based features improve ML accuracy. Rare fault simulation. False alarm filtering.
- **Build time:** Several months
- **Used for:** Process optimisation, cutting parameter tuning

### Level 3 — Full Digital Twin
- **Components:** Complete 3D CAD assembly + Physics solvers + ML + Real-time sync
- **CAD needed:** Yes — full assembly of all machine parts
- **What it does:** Complete virtual replica of the machine. Physics + ML work together in real time.
- **Build time:** 6–18 months, specialist software (ANSYS Twin Builder, Siemens NX)
- **Used for:** High-value machines, aerospace, automotive, defence

---

## 9. CAD Parts of a CNC Lathe

If building a Level 2 or Level 3 Digital Twin, these are the parts to model:

### Structural parts
| Part | Purpose in simulation |
|------|-----------------------|
| Machine bed | Backbone — vibration FEA, chatter propagation |
| Headstock + spindle | Bearing wear, spindle dynamics |
| Chuck (3-jaw/4-jaw) | Clamping force, imbalance vibration |

### Motion parts
| Part | Purpose in simulation |
|------|-----------------------|
| Carriage (saddle) | Z-axis motion, guideway wear |
| Cross-slide | X-axis motion, depth of cut control |
| Tool post + holder | Cutting force simulation, chatter stability |
| Tailstock | Support loads, alignment errors |

### Drive parts
| Part | Purpose in simulation |
|------|-----------------------|
| Lead screw / ball screw | Backlash, positioning error |
| Servo motors (X, Z) | Feed force, torque-speed simulation |
| Coolant nozzle + tank | Thermal simulation, CFD |

> **Assembly strategy:** Start with spindle + tool post + workpiece only (Level 1 minimum). Add carriage and ball screws for axis motion. Add full bed and headstock only for complete structural FEA.

---

## 10. How CAD and ML Work Together

In a Full Digital Twin, CAD and ML are not separate — they feed each other:

### Way 1 — Physics generates training data
FEA simulation at hundreds of operating conditions → virtual sensor readings → used as training data for rare faults the real machine has never produced safely

### Way 2 — Physics features as ML inputs
Natural frequencies and stiffness values extracted from FEA → added as input features to the ML model → model learns chatter risk relative to structural resonance, not just raw vibration amplitude

### Way 3 — Physics validates ML predictions
ML predicts a fault → physics model checks if it is physically possible at current operating conditions → filters out false positives before alerting the operator

**Analogy:**
- CAD/Physics = X-ray machine — shows internal structure, accurate but slow
- ML = heart rate monitor — watches continuously, fast but cannot explain physics
- Full Digital Twin = ICU — both running together for complete picture

---

## 11. Project Folder Structure

```
Digital_Twin_CNC/
│
├── cnc_lathe_dataset.csv       # Synthetic CNC lathe dataset (2000 samples, 21 features)
├── EDA.ipynb                   # Exploratory Data Analysis notebook
├── analysis_parameters.md      # This file — full project reference guide
├── README.md                   # Project overview
├── workflow.md                 # Step-by-step build workflow
│
├── models/                     # Trained ML models saved here
│   ├── fault_classifier.pkl
│   └── wear_regressor.pkl
│
├── notebooks/                  # All Jupyter notebooks
│   ├── 01_EDA.ipynb
│   ├── 02_fault_classification.ipynb
│   ├── 03_tool_wear_regression.ipynb
│   └── 04_RUL_prediction.ipynb
│
└── dashboard/                  # Monitoring dashboard code (future)
    └── app.py
```

---

## Quick Reference — Key Thresholds

| Parameter | Good | Worn | Critical |
|-----------|------|------|----------|
| Tool wear | < 0.15 mm | 0.15–0.30 mm | > 0.30 mm |
| Surface roughness Ra | < 1.6 μm | 1.6–3.2 μm | > 3.2 μm |
| Vibration magnitude | < 0.3 g | 0.3–0.8 g | > 0.8 g |
| Spindle temp | < 60°C | 60–80°C | > 80°C |
| Dimensional error | < 0.02 mm | 0.02–0.08 mm | > 0.08 mm |

---

*This file is part of the CNC Lathe Digital Twin project. Update this document as the project evolves.*
