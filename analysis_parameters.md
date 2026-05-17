# ⚙️ Analysis Parameters
### CNC Lathe Digital Twin — Settings & Configurations

> This document explains all the settings and choices made during the analysis of CNC Lathe machine data.  
> Written in simple language so anyone can understand — no coding knowledge required!

---

## 📌 Table of Contents

1. [What Tools Were Used](#1-what-tools-were-used)
2. [About the Dataset](#2-about-the-dataset)
3. [How the Data Was Cleaned](#3-how-the-data-was-cleaned)
4. [How the Charts Were Made](#4-how-the-charts-were-made)
5. [How the Machine Learning Model Works](#5-how-the-machine-learning-model-works)
6. [Alert Thresholds](#6-alert-thresholds)

---

## 1. What Tools Were Used

| Tool | What It Does |
|------|-------------|
| **Python 3.12** | The main programming language used |
| **Pandas** | Reads and organizes the data (like Excel but faster) |
| **NumPy** | Does mathematical calculations |
| **Matplotlib** | Creates charts and graphs |
| **Seaborn** | Makes the charts look clean and professional |
| **Scikit-learn** | Builds the machine learning prediction model |
| **Jupyter Notebook** | The environment where all the analysis was done |

---

## 2. About the Dataset

| Setting | Value | Why |
|---------|-------|-----|
| **File used** | `cnc_lathe_dataset.csv` | Raw sensor data from the CNC machine |
| **Training data** | 80% of the dataset | Used to teach the model |
| **Testing data** | 20% of the dataset | Used to check if the model is accurate |
| **What we're predicting** | Tool condition — `worn` or `unworn` | The main goal of this project |

### What the Machine Measures
These are the sensor readings collected from the CNC Lathe:

| Sensor Reading | What It Means |
|---------------|---------------|
| **Spindle Speed** | How fast the machine is spinning (RPM) |
| **Feed Rate** | How fast the material is being cut |
| **Depth of Cut** | How deep the tool goes into the material |
| **Vibration (X, Y, Z)** | Shaking/movement detected in 3 directions |
| **Temperature** | How hot the machine gets during cutting |
| **Power Consumption** | How much electricity the machine uses |

---

## 3. How the Data Was Cleaned

Before analyzing, the data was checked and prepared:

| Check | Result | Action Taken |
|-------|--------|-------------|
| **Missing values** | None found | No action needed ✅ |
| **Duplicate rows** | None found | No action needed ✅ |
| **Unusual extreme values** | Checked using IQR method | Flagged for review |
| **Text labels converted** | `worn` → 1, `unworn` → 0 | So the model can understand it |

### What is IQR? (Simple Explanation)
> Imagine lining up all temperature readings from lowest to highest.  
> IQR finds the **middle zone** of normal values.  
> Anything far outside this zone is flagged as unusual — like a temperature of 500°C when normal is 60°C.

---

## 4. How the Charts Were Made

All charts follow the same clean style so they are easy to read:

| Setting | Choice | Reason |
|---------|--------|--------|
| **Background style** | White grid | Easy to read values |
| **Chart size** | Wide (10×5 inches) | Fits all data clearly |
| **Color theme** | Soft distinct colors | Easy to tell categories apart |

### Charts Created in This Analysis

| Chart | What It Shows |
|-------|--------------|
| **Bar Chart** | How many worn vs unworn tool readings exist |
| **Heatmap** | Which sensors are related to each other |
| **Box Plot** | How sensor values differ between worn & unworn tools |
| **Histogram** | How each sensor reading is spread out |
| **Pair Plot** | All sensors compared against each other at once |

---

## 5. How the Machine Learning Model Works

### What Model Was Used?
**Random Forest** — think of it like asking 100 different experts their opinion, then going with the majority vote.

### Simple Breakdown

| Setting | Value | Plain English Meaning |
|---------|-------|-----------------------|
| **Number of experts (trees)** | 100 | 100 different decision paths are checked |
| **Validation method** | 5-Fold Cross Validation | Data is tested 5 different ways to ensure fairness |
| **Reproducibility seed** | 42 | Ensures the same results every time the code runs |

### How Do We Know If the Model Is Good?

| Measure | What It Means |
|---------|--------------|
| **Accuracy** | Out of all predictions, how many were correct? |
| **Precision** | When it says "worn", how often is it actually right? |
| **Recall** | Out of all actually worn tools, how many did it catch? |
| **F1 Score** | A balance between precision and recall |

---

## 6. Alert Thresholds

These are the danger levels set for the Digital Twin monitoring system.  
When sensor readings cross these levels, an alert is triggered.

### 🚦 Tool Health Traffic Light System

| Sensor | 🟢 Normal | 🟡 Warning | 🔴 Critical |
|--------|----------|-----------|------------|
| **Vibration** | Below 0.5g | 0.5g – 1.0g | Above 1.0g |
| **Temperature** | Below 60°C | 60°C – 80°C | Above 80°C |
| **Power Usage** | Normal baseline | 10–20% above normal | More than 20% above normal |

### What Happens at Each Level?

| Level | Meaning | Recommended Action |
|-------|---------|-------------------|
| 🟢 **Normal** | Machine is healthy | Continue operation |
| 🟡 **Warning** | Early signs of wear | Monitor more closely |
| 🔴 **Critical** | Tool is likely worn out | Stop and replace the tool |

---

## 📝 Quick Summary

- **80%** of data used for training, **20%** for testing
- **Vibration, Temperature & Power** are the top 3 warning signs of tool wear
- **100 decision trees** work together to predict tool condition
- Alerts trigger automatically when readings cross safe thresholds
- All results are **reproducible** — running the notebook again gives the same output

---

> 💡 **Tip:** You don't need to understand the code to use this system.  
> Just watch the 🟢 🟡 🔴 indicators — they tell you everything you need to know!
