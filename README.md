# 🔧 Digital Twin — CNC Lathe Monitor

> A data-driven Digital Twin system for CNC Lathe machines — enabling real-time condition monitoring, tool wear prediction, and predictive maintenance using machine learning.

---

## 📌 Table of Contents

- [Overview](#-overview)
- [Project Structure](#-project-structure)
- [Dataset](#-dataset)
- [Tech Stack](#-tech-stack)
- [Getting Started](#-getting-started)
- [Analysis Workflow](#-analysis-workflow)
- [Results & Insights](#-results--insights)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🧠 Overview

This project builds a **Digital Twin** of a CNC Lathe machine — a virtual replica that mirrors the real machine's behavior using sensor data. The goal is to:

- 📊 **Monitor** machine health in real time
- 🔍 **Detect** anomalies and abnormal tool conditions
- 🔮 **Predict** tool wear and remaining useful life (RUL)
- 🛠️ **Enable** predictive maintenance before failures occur

A Digital Twin reduces unplanned downtime, extends tool life, and improves manufacturing efficiency.

---

## 📁 Project Structure

```
Digital_Twin_CNC/
│
├── 📂 data/
│   └── cnc_lathe_dataset.csv        # Raw sensor data from CNC lathe
│
├── 📂 notebooks/
│   └── EDA.ipynb                   # Main analysis notebook
│
├── 📂 models/                       # Trained ML models (if applicable)
│   └── trained_model.pkl
│
├── 📂 reports/
│   └── figures/                     # Generated plots and charts
│
├── analysis_parameters.md           # Parameters used in analysis
├── workflow.md                      # Step-by-step methodology
├── requirements.txt                 # Python dependencies
├── .gitignore                       # Files excluded from version control
├── LICENSE                          # MIT License
└── README.md                        # Project documentation (this file)
```

---

## 📊 Dataset

**File:** `cnc_lathe_dataset.csv`

The dataset contains sensor readings collected from a CNC Lathe machine during operation.

| Feature | Description |
|--------|-------------|
| `tool_condition` | Current state of the cutting tool (worn / unworn) |
| `spindle_speed` | RPM of the lathe spindle |
| `feed_rate` | Material feed rate (mm/min) |
| `depth_of_cut` | Cutting depth (mm) |
| `vibration_x/y/z` | Vibration sensor readings across axes |
| `temperature` | Machine operating temperature (°C) |
| `power_consumption` | Electrical power drawn (W) |

> **Note:** Dataset may contain additional features depending on the sensor configuration used.

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| ![Python](https://img.shields.io/badge/Python-3.12-blue?logo=python) | Core programming language |
| ![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-lightblue?logo=pandas) | Data manipulation |
| ![NumPy](https://img.shields.io/badge/NumPy-Numerical%20Computing-013243?logo=numpy) | Numerical operations |
| ![Matplotlib](https://img.shields.io/badge/Matplotlib-Visualization-orange) | Plotting & charts |
| ![Seaborn](https://img.shields.io/badge/Seaborn-Statistical%20Plots-teal) | Statistical visualization |
| ![Scikit-learn](https://img.shields.io/badge/Scikit--learn-ML%20Models-F7931E?logo=scikit-learn) | Machine learning |
| ![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter) | Interactive analysis |

---

## 🚀 Getting Started

### 1. Clone the Repository
```bash
git clone https://github.com/ssjha210/Digital_Twin_CNC.git
cd Digital_Twin_CNC
```

### 2. Create a Virtual Environment
```bash
python -m venv .venv

# Activate (Windows)
.venv\Scripts\activate

# Activate (Mac/Linux)
source .venv/bin/activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

### 4. Launch Jupyter Notebook
```bash
jupyter notebook notebooks/data.ipynb
```

---

## 🔄 Analysis Workflow

```
Raw CNC Sensor Data (CSV)
        ↓
1️⃣  Data Loading & Exploration
        ↓
2️⃣  Data Cleaning & Preprocessing
        ↓
3️⃣  Exploratory Data Analysis (EDA)
        ↓
4️⃣  Feature Engineering
        ↓
5️⃣  Model Training & Evaluation
        ↓
6️⃣  Tool Condition Prediction
        ↓
Digital Twin Insights & Alerts ✅
```

See [`workflow.md`](workflow.md) for detailed step-by-step methodology.

---

## 📈 Results & Insights

- ✅ Identified key features correlated with tool wear
- ✅ Visualized tool condition distribution across operating parameters
- ✅ Built classification model to predict tool condition (worn/unworn)
- ✅ Detected anomaly patterns from vibration and temperature data

> Detailed results and charts are available inside [`data.ipynb`](notebooks/data.ipynb)

---

## 🤝 Contributing

Contributions are welcome! Here's how:

1. Fork the repository
2. Create a new branch: `git checkout -b feature/your-feature`
3. Make your changes and commit: `git commit -m "Add your feature"`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

**Shanu Jha**
- GitHub: [@ssjha210](https://github.com/ssjha210)

---

> ⭐ If you found this project helpful, please give it a star on GitHub!
