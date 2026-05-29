# 🔄 Project Workflow — CNC Lathe Digital Twin

> A simple explanation of how this Digital Twin was built — from idea to working system.  
> For settings, thresholds, and model details → see `analysis_parameters.md`

---

## 📌 Table of Contents

1. [What is a Digital Twin?](#1-what-is-a-digital-twin)
2. [The Problem We Are Solving](#2-the-problem-we-are-solving)
3. [How the System Works](#3-how-the-system-works)
4. [Phase 1 — Data Collection](#4-phase-1--data-collection)
5. [Phase 2 — Data Analysis](#5-phase-2--data-analysis)
6. [Phase 3 — Building the Model](#6-phase-3--building-the-model)
7. [Phase 4 — Digital Twin Monitoring](#7-phase-4--digital-twin-monitoring)
8. [Key Findings](#8-key-findings)
9. [Future Plans](#9-future-plans)

---

## 1. What is a Digital Twin?

A **Digital Twin** is a virtual copy of a real machine that:
- Continuously receives live data from the real machine
- Learns the machine's normal and abnormal behavior
- Predicts problems **before** they physically happen
- Helps operators make smarter maintenance decisions

### Simple Analogy
> Think of it like a **smartwatch for a factory machine.**  
> Just like a smartwatch monitors your heart rate and warns you of health issues —  
> our Digital Twin monitors the CNC Lathe and warns operators before the cutting tool fails. 🏭

---

## 2. The Problem We Are Solving

CNC Lathe machines use **cutting tools** that wear out after extended use.

| Situation | What Goes Wrong |
|-----------|----------------|
| Tool wears out unnoticed | Poor quality parts are produced |
| Tool breaks mid-operation | Machine itself gets damaged |
| Tool replaced too early | Unnecessary cost and waste |
| Tool replaced too late | Production stops unexpectedly |

### Our Solution
Use sensor data + machine learning to predict tool wear **before it becomes a problem** — so operators replace the tool at exactly the right time.

---

## 3. How the System Works

```
CNC Lathe Machine runs a cutting operation
                ↓
Sensors record live data during every cut
                ↓
Data is stored and sent to the Digital Twin
                ↓
Machine Learning model analyzes the data
                ↓
Model predicts: Is the tool Worn or Unworn?
                ↓
If worn → Alert is triggered immediately
                ↓
Operator replaces the tool at the right time ✅
```

---

## 4. Phase 1 — Data Collection

### How Data Was Collected
Multiple industrial sensors were attached to the CNC Lathe to record readings during every cutting operation. A human inspector then labeled each record as **worn** or **unworn** based on physical tool inspection.

### Sensors Used & Their Role

| Sensor Name | Type | Measures | Why It Matters |
|-------------|------|----------|---------------|
| **ADXL345** | Accelerometer | Vibration on X, Y, Z axes | Worn tools vibrate more than sharp ones |
| **MLX90614** | Infrared Thermometer | Cutting zone temperature (°C) | Friction rises as tool dulls |
| **ACS712** | Current Sensor | Power consumption (Watts) | Worn tools draw more electricity |
| **Optical Encoder** | Rotary Encoder | Spindle speed (RPM) | Tracks machine rotation rate |
| **Linear Encoder** | Position Sensor | Feed rate & depth of cut (mm) | Monitors cutting aggressiveness |
| **Visual Camera** | Camera Module | Surface finish of the cut part | Detects visible quality defects |

### What Each Record Contains
Every row in `cnc_lathe_dataset.csv` represents one cutting operation and includes:
- All sensor readings captured during that cut
- A label — **worn** or **unworn** — added by the human inspector after the cut

---

## 5. Phase 2 — Data Analysis

### Goal of This Phase
Understand the data deeply before building any model — find which sensors are most useful for detecting tool wear.

### What Was Done

**Explored the data** — checked how many worn vs unworn records exist, confirmed the dataset is balanced and reliable.

**Found patterns visually** — created charts to compare how sensor readings differ between worn and unworn tools. Key discovery: worn tools show clearly higher vibration and temperature readings.

**Measured relationships** — calculated which sensors are most strongly connected to tool wear. Vibration came out as the top predictor, followed by temperature and power consumption.

### Most Important Discovery
> When a tool starts wearing out, the **ADXL345 accelerometer** picks it up first.  
> Vibration increases 2 to 3 times compared to a sharp tool — making it the earliest and most reliable warning sign.

---

## 6. Phase 3 — Building the Model

### Goal of This Phase
Train a machine learning model that can look at sensor readings and automatically decide — **worn or unworn?**

### Why Random Forest Was Chosen
Random Forest was selected because it works exceptionally well with sensor data, handles noise from real-world readings, and clearly shows which sensors matter most for the prediction.

> Imagine asking 100 engineers to each look at the sensor data independently and give their verdict.  
> The final answer is whichever verdict the majority agrees on.  
> That is exactly how Random Forest works — 100 decision trees voting together. 🗳️

### How the Model Was Trained

| Stage | What Happened |
|-------|--------------|
| Data split | 80% used for training, 20% kept aside for testing |
| Training | Model studied patterns in the 80% training data |
| Testing | Model made predictions on the unseen 20% test data |
| Evaluation | Predictions compared against real labels to measure accuracy |

### What the Model Learned
The model discovered that **vibration, temperature, and power consumption** together are strong enough to reliably predict tool condition — even before visible wear appears on the tool surface.

---

## 7. Phase 4 — Digital Twin Monitoring

### How the Digital Twin Uses the Model
Once the model is trained, it becomes the brain of the Digital Twin system:

| Step | What Happens |
|------|-------------|
| Sensors stream live data every second | ADXL345, MLX90614, ACS712 all send readings continuously |
| Digital Twin receives the data | Every new reading is instantly processed |
| Model makes a prediction | Worn or Unworn — decided in real time |
| System checks alert thresholds | Is vibration, temperature, or power too high? |
| Alert sent if needed | Operator notified before tool failure occurs |

### What the Operator Sees
- Live readings from all sensors on a monitoring screen
- Current tool condition status — healthy or at risk
- A trend graph showing how wear is progressing over time
- An alert notification when it is time to replace the tool

---

## 8. Key Findings

| Finding | Detail |
|---------|--------|
| **Best wear indicator** | Vibration detected by ADXL345 (X and Z axes) |
| **Second best indicator** | Temperature from MLX90614 infrared sensor |
| **Third indicator** | Power draw measured by ACS712 current sensor |
| **Early detection** | Wear detectable before visible damage appears on tool |
| **Main benefit** | Right-time tool replacement — not too early, not too late |

---

## 9. Future Plans

- [ ] Connect sensors directly to a live CNC machine for real-time streaming
- [ ] Build a live dashboard showing all sensor readings and tool health status
- [ ] Set up automatic SMS or email alerts when tool wear is predicted
- [ ] Predict exactly how many cuts remain before the tool needs replacement
- [ ] Expand the system to monitor multiple CNC machines simultaneously

---

> 💡 For all settings, thresholds, model parameters, and chart details — see [`analysis_parameters.md`](analysis_parameters.md)  
> For project setup and installation — see [`README.md`](README.md)
