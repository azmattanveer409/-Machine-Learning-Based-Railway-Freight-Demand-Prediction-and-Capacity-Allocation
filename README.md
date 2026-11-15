# 🚆 Machine Learning-Based Railway Freight Demand Prediction and Capacity Allocation



![Python](https://img.shields.io/badge/Python-3.10%2B-blue)




![Status](https://img.shields.io/badge/Project-Research%20Prototype-orange)




![Forecasting](https://img.shields.io/badge/Forecasting-Random%20Forest%20%7C%20XGBoost-green)




![Optimization](https://img.shields.io/badge/Optimization-Linear%20Programming%20(PuLP)

-purple)


![Data](https://img.shields.io/badge/Data-UK%20ORR%20Freight%20Statistics-lightgrey)




![License](https://img.shields.io/badge/License-MIT-yellow)



> **A beginner-friendly research prototype that combines historical railway freight statistics, machine-learning demand forecasting, and a small capacity-allocation optimisation model.**

---

## 📌 Project Overview

Railway planners need to understand **how much freight may need to be moved** and **how limited transport resources can be used efficiently**. This project explores a simple, data-driven workflow:

1. 📥 Examine historical freight statistics published by the UK **Office of Rail and Road (ORR)**.
2. 📈 Forecast aggregate freight transport activity using a **naïve baseline**, **Random Forest** and **XGBoost**.
3. 🔗 Use the Random Forest prediction results as an input to a small **linear-programming** allocation example.
4. 🚦 Examine how illustrative route capacity and demand scenarios affect allocation and unmet demand.

> ⚠️ **This is an academic prototype, not a live railway planning system.** The ORR data are aggregate statistics. The three route capacities and route costs used in the optimisation are **illustrative assumptions**, not observed railway operating data.

---

## 🎯 Research Question

> **How can machine-learning-based freight demand forecasts be used to inform a capacity-constrained railway freight allocation model?**

---

## ⭐ Key Results at a Glance

| Item | Result |
|---|---|
| Best forecasting model (this test split) | **Random Forest** |
| Random Forest test error | MAE **48.590** · RMSE **79.362** · WAPE **3.828%** |
| Improvement over naïve baseline (MAE) | 91.441 → 48.590 |
| Most influential feature (Random Forest) | **`lag_13`** |
| Demonstration optimisation | Solver status **Optimal**, unmet demand **0** |
| Integrated forecast → optimisation | 1,243.270 units allocated, unmet demand **0** |

---

## 🧩 Workflow

```text
ORR freight statistics
        |
        v
Data inspection and exploratory analysis
        |
        v
Historical freight series
        |
        v
Lag features and chronological train/test split
        |
        v
Naive baseline vs Random Forest vs XGBoost
        |
        v
Random Forest test predictions
        |
        v
Illustrative planning-demand estimate
        |
        v
Linear programming capacity allocation
        |
        v
Scenario / sensitivity analysis
```

---

## 📂 Repository Structure

```text
Railway-Freight-Forecasting-Optimization/
│
├── README.md
├── LICENSE
├── CITATION.cff
├── requirements.txt
├── .gitignore
│
├── notebooks/
│   └── Railway_Freight_Forecasting_Optimization.ipynb
│
├── data/
│   └── README.md                  # how to obtain the ORR CSV files
│
└── assets/
    ├── 01_historical_freight_movement.png
    ├── 02_forecast_model_comparison.png
    ├── 03_random_forest_feature_importance.png
    └── 04_capacity_utilisation_scenarios.png
```

---

## 📊 Data

The notebook uses two ORR freight-statistics tables:

- **Table 1314 – Freight moved by commodity (periodic):** used to create the aggregate freight time series for forecasting.
- **Table 1311 – Freight moved by Network Rail region (periodic):** inspected during the exploratory data-analysis stage.

**Forecasting target:** `Total (million net tonne kilometers)`

| Series detail | Value |
|---|---|
| Observations in the selected series | **98** |
| Modelling observations (after lag features) | **85** |
| Training observations | **68** |
| Test observations | **17** |

> ⚠️ The measure is **million net tonne-kilometres** (freight transport work), not simply tonnes of goods. Keep this distinction when describing the project.

### 📥 Data access

The original CSV files are **not included** in this repository. Download the tables from the ORR data portal and place them where the notebook can read them. The notebook refers to:

- `Table 1314_ Freight moved by commodity (periodic).csv`
- `Table 1311_ Freight moved by Network Rail region (periodic).csv`

See [`data/README.md`](data/README.md) for step-by-step details.

---

## 🧠 Forecasting Method

The notebook creates the lag features `lag_1`, `lag_2`, `lag_3`, `lag_4` and `lag_13`, plus a previous-four-period rolling mean. It uses a **chronological 80/20 split** rather than shuffling time-series observations.

| Approach | Idea in simple words |
|---|---|
| **Naïve baseline** | Predicts the next value as the previous period's value |
| **Random Forest** | Averages the predictions of many decision trees |
| **XGBoost** | Builds boosted trees one after another to reduce errors |

### 📉 Forecasting Results

| Model | MAE | RMSE | WAPE |
|---|---|---|---|
| Naïve baseline | 91.441 | 151.076 | 7.204% |
| **Random Forest** | **48.590** | **79.362** | **3.828%** |
| XGBoost | 55.352 | 82.151 | 4.361% |

**Result in this test split:** Random Forest had the lowest MAE, RMSE and WAPE of the three approaches. These are hold-out results for this dataset and split. They do **not** prove that Random Forest will always outperform the alternatives.

### 📈 Historical Freight Movement



![Historical Freight Movement](assets/01_historical_freight_movement.png)



### 🔍 Forecast Comparison




<img width="1389" height="590" alt="download" src="https://github.com/user-attachments/assets/0b73b92c-00ef-4d77-b564-e170fa6031a0" />



### 🌲 Random Forest Feature Importance



<img width="889" height="490" alt="download" src="https://github.com/user-attachments/assets/8703d0cf-719c-4f4b-a5f9-6aee5b53ed44" />



`lag_13` is the most influential feature in this fitted Random Forest model. It represents the freight value 13 observations earlier. Feature importance describes how the model *used* a feature; it does **not** demonstrate a causal relationship.

---

## 🚦 Optimisation Method

The notebook uses **linear programming** with **PuLP**. In simple terms, the model decides how much freight to allocate to each of three illustrative routes while respecting every route's capacity.

**Objective:** minimise

```text
sum(route cost × allocated freight) + unmet-demand penalty × unmet demand
```

**Subject to:**

- ✅ Allocation on each route cannot exceed its stated capacity.
- ✅ Allocations cannot be negative.
- ✅ Allocated freight + unmet demand = demand supplied to the model.

### 🛤️ Illustrative Network Assumptions

| Route | Capacity (illustrative units) | Cost per unit (illustrative) |
|---|---|---|
| Route A | 500 | 10 |
| Route B | 700 | 12 |
| Route C | 600 | 9 |
| **Total** | **1,800** | — |

> These are demonstration inputs. They are **not** actual ORR route capacities, actual railway costs, or verified route-level data.

### ✅ Demonstration Result (demand = 1,200)

| Route | Allocated freight |
|---|---|
| Route A | 500 |
| Route B | 100 |
| Route C | 600 |
| **Total allocated** | **1,200** |

- Solver status: **Optimal**
- Unmet demand: **0**
- Objective value: **11,600 illustrative cost units**

The model fills the cheapest route first (C, then A) and uses the most expensive route (B) only for the remainder.

---

## 🔗 Forecasting-to-Optimisation Connection

The notebook averages the 17 Random Forest test predictions and passes that value to the optimisation model:

| Item | Value |
|---|---|
| Mean predicted planning-demand proxy | **1,243.270** |
| Test-period predictions averaged | 17 |
| Illustrative total route capacity | 1,800 |
| Integrated solver status | **Optimal** |
| Freight allocated | 1,243.270 |
| Unmet demand | 0 |
| Objective value | 12,119.242 illustrative cost units |

> **Important interpretation:** 1,243.270 is the mean of predictions over the held-out test period. It is a **planning proxy** for the demonstration, **not** a separate forecast for a new future period. The ORR forecast measure and the illustrative route capacities must also be converted to compatible operational units before any real-world interpretation. This output demonstrates the **workflow**, not a deployable railway allocation plan.

---

## 📉 Sensitivity Analysis

Four demand scenarios are tested against the illustrative total capacity of 1,800:

| Demand scenario | Allocated | Unmet | Capacity utilisation |
|---|---|---|---|
| 900 | 900 | 0 | 50.0% |
| 1,200 | 1,200 | 0 | 66.7% |
| 1,600 | 1,600 | 0 | 88.9% |
| 1,900 | 1,800 | 100 | 100.0% |



[Capacity Utilisation Scenarios]
<img width="890" height="490" alt="download" src="https://github.com/user-attachments/assets/1abe3d3c-7bdf-47f3-ac41-bda8ee0d22fc" />




The final scenario exceeds total illustrative capacity, so **100 units remain unmet**.

---

## 🛠️ Tools and Libraries

| Tool | Use |
|---|---|
| 🐍 **Python** | Programming language |
| 🐼 **pandas / NumPy** | Data handling and numerical work |
| 📊 **Matplotlib** | Visualisation |
| 🤖 **scikit-learn** | Random Forest and evaluation metrics |
| ⚡ **XGBoost** | Gradient-boosted tree model |
| 🧮 **PuLP** | Linear-programming optimisation |
| ☁️ **Google Colab / Jupyter** | Development environment |

---

## ▶️ How to Run

### Option A — Google Colab ☁️

1. Download or clone this repository.
2. Upload `notebooks/Railway_Freight_Forecasting_Optimization.ipynb` to Google Colab.
3. Obtain the two ORR CSV files described in [`data/README.md`](data/README.md).
4. Upload the CSV files to the Colab session, or update the paths in the notebook.
5. Run the cells from top to bottom.
6. Review the printed tables and generated plots.

### Option B — Local Python environment 💻

```bash
git clone https://github.com/azmattanveer409/-Machine-Learning-Based-Railway-Freight-Demand-Prediction-and-Capacity-Allocation.git
cd -- -Machine-Learning-Based-Railway-Freight-Demand-Prediction-and-Capacity-Allocation

python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

pip install -r requirements.txt
jupyter notebook notebooks/Railway_Freight_Forecasting_Optimization.ipynb
```

Make sure the two CSV files are available at the paths used in the notebook.

---

## ⚠️ Limitations and Responsible Interpretation

1. Forecast performance is measured on **one** chronological hold-out split. Rolling-origin or expanding-window validation would give stronger evidence.
2. The series has only **98 observations**, a relatively small sample.
3. The planning demand is the **mean of test-period predictions**, not a separately generated future-horizon forecast.
4. ORR Table 1314 is an aggregate measure; it does not identify actual demand on each of the three illustrative routes.
5. Route capacities and costs are **assumptions**, not measured operational values.
6. Demand and capacity must be in **compatible units** before a practical allocation can be interpreted.
7. The model has no real network topology, train paths, terminals, timetables, commodity-specific requirements or disruption risks.
8. The unmet-demand penalty influences the optimisation result and would need evidence-based calibration in an applied study.

---

## 🔭 Possible Next Improvements

- [ ] Validate the forecasting model with expanding-window time-series testing.
- [ ] Generate a genuine future-period forecast instead of averaging test predictions.
- [ ] Obtain route-level demand, capacity and cost data from a railway operator or suitable open source.
- [ ] Check that demand and capacity use compatible units.
- [ ] Extend the linear programme only when reliable operational data justify it.

---

## 🎓 Research Relevance

The project has a **thematic connection** with research on transport optimisation, data mining and AI-driven transport planning, including the work of Professor Yimeng Zhang at the School of Transportation and Logistics, Southwest Jiaotong University (multimodal and synchromodal freight planning, railway logistics).

This student project is much simpler than that advanced research. Its foundation is the combination of **data-driven demand estimation** and **optimisation under capacity constraints**. It should be read as an introductory research prototype and a basis for further study, **not** as a reproduction of any published method or a validated railway operating system.

**Useful official references:**

- [Professor Yimeng Zhang's academic homepage](https://yimengzhang-swjtu.github.io/personal-website/)
- [Southwest Jiaotong University faculty profile](https://faculty.swjtu.edu.cn/zhangyimeng/zh_CN/index.htm)
- [Operational synchromodal transport planning methodologies: Review and roadmap](https://doi.org/10.1016/j.tre.2024.103915)

---

## 📚 Learning Outcomes

After working through this project, a student should be able to explain:

- How to inspect and prepare a time-series dataset.
- What lag features represent.
- How a chronological train/test split works.
- The basic difference between a naïve baseline, Random Forest and XGBoost.
- What MAE, RMSE and WAPE measure.
- What a decision variable, objective function and capacity constraint mean.
- How a forecast can be passed into an optimisation model.
- Why assumptions and data limitations matter in research.

---

## 📄 Main Project File

The complete executed notebook: [`notebooks/Railway_Freight_Forecasting_Optimization.ipynb`](notebooks/Railway_Freight_Forecasting_Optimization.ipynb)

---

## 👤 Author

**Azmat Tanveer**
BS Mathematics — Abbottabad University of Science and Technology, Pakistan

🔗 GitHub: [@azmattanveer409](https://github.com/azmattanveer409)

---

## 📝 Citation and Data Source

If you use or adapt this educational research prototype, please:

- cite the **UK Office of Rail and Road (ORR)** as the data source, and
- clearly distinguish **observed data** from **illustrative assumptions**.

A machine-readable citation is available in [`CITATION.cff`](CITATION.cff).

---

⭐ *If you find this project useful, consider starring the repository.*
