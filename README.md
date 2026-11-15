# 🚆 Machine Learning-Based Railway Freight Demand Prediction and Capacity Allocation

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Status](https://img.shields.io/badge/Project-Research%20Prototype-orange)
![Methods](https://img.shields.io/badge/Methods-Random%20Forest%20%7C%20XGBoost%20%7C%20Linear%20Programming-green)

> **A beginner-friendly research prototype that combines historical railway freight statistics, machine-learning demand forecasting, and a small capacity-allocation optimisation model.**

---

## 📌 Project overview

Railway planners need to understand how much freight may need to be transported and how limited transport resources can be used efficiently. This project explores a simple data-driven workflow:

1. Examine historical freight statistics published by the UK Office of Rail and Road (ORR).
2. Forecast aggregate freight transport activity using a naïve baseline, Random Forest, and XGBoost.
3. Use the Random Forest prediction results as an input to a small linear-programming allocation example.
4. Examine how illustrative route capacity and demand scenarios affect allocation and unmet demand.

The project is an **academic prototype**, not a live railway planning system. The ORR data are aggregate statistics. The three route capacities and route costs used in the optimisation are illustrative assumptions, not observed railway operating data.

## 🎯 Research question

**How can machine-learning-based freight demand forecasts be used to inform a capacity-constrained railway freight allocation model?**

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

## 📂 Repository structure

```text
railway-freight-forecasting-optimization/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── notebooks/
│   └── Railway_Freight_Forecasting_Optimization.ipynb
│
├── data/
│   └── README.md
│
├── assets/
│   ├── historical_freight_movement.png
│   ├── actual_vs_predicted_stage3.png
│   ├── random_forest_feature_importance.png
│   ├── actual_vs_random_forest.png
│   └── capacity_utilisation_scenarios.png
│
└── docs/
    └── PROJECT_EXPLAINER_PROMPT.md
```

## 📊 Data

The notebook uses two ORR freight-statistics tables:

- **Table 1314 – Freight moved by commodity (periodic):** used to create the aggregate freight time series for forecasting.
- **Table 1311 – Freight moved by Network Rail region (periodic):** inspected during the exploratory data-analysis stage.

The forecasting target is the column:

`Total (million net tonne kilometers)`

The notebook reports **98 observations** in the selected series. After creating lag and rolling-average features and removing rows with missing lag values, it reports **85 modelling observations**, divided chronologically into **68 training observations and 17 test observations**.

> ⚠️ The measure is million net tonne-kilometres (a measure of freight transport work), not simply tonnes of goods. Keep this distinction when describing the project.

### Data access and setup

The original CSV files are not included in this repository. Obtain the relevant tables from the ORR data portal and place the CSV files where the notebook can access them. The notebook currently refers to these filenames:

- `Table 1314_ Freight moved by commodity (periodic).csv`
- `Table 1311_ Freight moved by Network Rail region (periodic).csv`

See [`data/README.md`](data/README.md) for details. Do not commit restricted, confidential, or personally sensitive data.

## 🧠 Forecasting method

The notebook creates lag features (`lag_1`, `lag_2`, `lag_3`, `lag_4`, `lag_13`) and a previous-four-period rolling mean. It uses a chronological 80/20 split rather than randomly shuffling time-series observations.

Three approaches are compared:

- **Naïve baseline:** uses the previous period's freight value as the next prediction.
- **Random Forest:** combines multiple decision trees and averages their predictions.
- **XGBoost:** builds boosted decision trees sequentially to reduce prediction errors.

### Forecasting results

| Model | MAE | RMSE | WAPE |
|---|---:|---:|---:|
| Naïve baseline | 91.441 | 151.076 | 7.204% |
| **Random Forest** | **48.590** | **79.362** | **3.828%** |
| XGBoost | 55.352 | 82.151 | 4.361% |

**Result in this test split:** Random Forest had the lowest MAE, RMSE, and WAPE of the three compared approaches. These are hold-out test results from this dataset and split; they do not prove that Random Forest will always outperform the alternatives.

### 📈 Forecast comparison

<img width="1389" height="590" alt="download" src="https://github.com/user-attachments/assets/0b73b92c-00ef-4d77-b564-e170fa6031a0" />


### 🌲 Random Forest feature importance


<img width="889" height="490" alt="download" src="https://github.com/user-attachments/assets/8703d0cf-719c-4f4b-a5f9-6aee5b53ed44" />


`lag_13` is the most influential feature in this fitted Random Forest model. It represents the freight value 13 observations earlier. Feature importance describes the model's use of a feature; it does not demonstrate a causal relationship.

## 🚦 Optimisation method

The notebook uses **linear programming** with PuLP. In simple terms, the model chooses how much freight to allocate to each of three illustrative routes while respecting each route's capacity.

The optimisation objective is to minimise:

`sum(route cost × allocated freight) + unmet-demand penalty × unmet demand`

Subject to:

- Allocation on each route cannot exceed its stated capacity.
- Allocations cannot be negative.
- Allocated freight plus unmet demand must equal the demand supplied to the model.

### Illustrative network assumptions

| Route | Capacity (illustrative units) | Cost per unit (illustrative cost units) |
|---|---:|---:|
| Route A | 500 | 10 |
| Route B | 700 | 12 |
| Route C | 600 | 9 |
| **Total** | **1,800** | — |

These are demonstration inputs. They are **not** actual ORR route capacities, actual railway costs, or verified route-level data.

### Demonstration optimisation result

For the notebook's demonstration demand of 1,200:

| Route | Allocated freight |
|---|---:|
| Route A | 500 |
| Route B | 100 |
| Route C | 600 |
| **Total allocated** | **1,200** |

- Solver status: **Optimal**
- Unmet demand: **0**
- Objective value: **11,600 illustrative cost units**

## 🔗 Forecasting-to-optimisation connection

The notebook also calculates the mean of the 17 Random Forest test predictions:

- Mean predicted planning-demand proxy: **1,243.270**
- Number of test-period predictions averaged: **17**
- Illustrative total route capacity: **1,800**
- Integrated solver status: **Optimal**
- Freight allocated: **1,243.270**
- Unmet demand: **0**
- Objective value: **12,119.242 illustrative cost units**

**Important interpretation:** 1,243.270 is the mean of predictions over the held-out test period. It is a planning proxy used in the demonstration, **not a separate forecast for a new future period**. The aggregate ORR forecast measure and the illustrative route capacities also need to be converted to compatible operational units before any real-world interpretation. Therefore, this integrated output demonstrates the workflow, not a deployable railway allocation plan.

## 📉 Sensitivity analysis

The notebook tests four demand scenarios against the illustrative total capacity of 1,800:

| Demand scenario | Allocated | Unmet | Capacity utilisation |
|---:|---:|---:|---:|
| 900 | 900 | 0 | 50.0% |
| 1,200 | 1,200 | 0 | 66.7% |
| 1,600 | 1,600 | 0 | 88.9% |
| 1,900 | 1,800 | 100 | 100.0% |

<img width="890" height="490" alt="download" src="https://github.com/user-attachments/assets/1abe3d3c-7bdf-47f3-ac41-bda8ee0d22fc" />


The final scenario exceeds the total illustrative capacity, so 100 units remain unmet.

## 🛠️ Tools and libraries

- Python
- pandas and NumPy
- Matplotlib
- scikit-learn (Random Forest and evaluation metrics)
- XGBoost
- PuLP (linear programming)

## ▶️ How to run the project

### Option A — Google Colab

1. Download or clone this repository.
2. Upload `notebooks/Railway_Freight_Forecasting_Optimization.ipynb` to Google Colab.
3. Obtain the two ORR CSV files listed in `data/README.md`.
4. Upload the CSV files into the Colab session, or update the notebook paths to their actual locations.
5. Run the notebook cells from top to bottom.
6. Review the printed tables and generated plots.

### Option B — Local Python environment

```bash
git clone https://github.com/YOUR-USERNAME/railway-freight-forecasting-optimization.git
cd railway-freight-forecasting-optimization

python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

pip install -r requirements.txt
```

Then open the notebook:

```bash
jupyter notebook notebooks/Railway_Freight_Forecasting_Optimization.ipynb
```

Make sure the two CSV files are accessible at the paths used in the notebook.

## ⚠️ Limitations and responsible interpretation

1. Forecast performance is measured on one chronological hold-out split. Additional rolling-origin or expanding-window validation would give stronger evidence.
2. The forecasting series contains 98 observations, so the sample is relatively small.
3. The planning demand is the mean of test-period predictions, not a separately generated future-horizon forecast.
4. ORR Table 1314 provides an aggregate freight measure; it does not identify actual demand on each of the three illustrative routes.
5. Route capacities and costs are assumptions created for the optimisation demonstration, not measured operational values.
6. Freight demand and route capacity must be expressed in compatible units before a practical allocation can be interpreted.
7. The model does not include a real railway network topology, train paths, terminals, timetables, commodity-specific requirements, or disruption risks.
8. The unmet-demand penalty affects the optimisation result and would need evidence-based calibration in an applied study.

## 🔭 Possible next improvements

Keep future work focused and understandable:

- Validate the forecasting model with expanding-window time-series testing.
- Generate a genuine future-period forecast rather than averaging test predictions.
- Obtain route-level demand, capacity, and cost data from a railway operator or a suitable open source.
- Check that demand and capacity use compatible units.
- Extend the small linear programme only when reliable operational data justify doing so.

## 👨‍🏫 Relevance to Professor Yimeng Zhang's research

The project has a **thematic connection** with Professor Yimeng Zhang's work at the School of Transportation and Logistics, Southwest Jiaotong University. His official profile describes research in multimodal transport optimisation, data mining, reinforcement learning, and AI-driven transport planning. His work also includes synchromodal freight planning and railway logistics projects.

This student project is much simpler than his advanced research. Its relevant foundation is the combination of data-driven demand estimation and optimisation under capacity constraints. It should be presented as an introductory research prototype and a basis for further study—not as a reproduction of his methods or a validated railway operating system.

Useful official references:
- [Professor Yimeng Zhang's academic homepage](https://yimengzhang-swjtu.github.io/personal-website/)
- [Southwest Jiaotong University faculty profile](https://faculty.swjtu.edu.cn/zhangyimeng/zh_CN/index.htm)
- [Operational synchromodal transport planning methodologies: Review and roadmap](https://doi.org/10.1016/j.tre.2024.103915)

## 📚 Learning outcomes

By completing and explaining this project, a student should be able to describe:

- How to inspect and prepare a time-series dataset.
- What lag features represent.
- How a chronological train/test split works.
- The basic difference between a naïve baseline, Random Forest, and XGBoost.
- What MAE, RMSE, and WAPE measure.
- What a decision variable, objective function, and capacity constraint mean.
- How a forecast can be passed into an optimisation model.
- Why assumptions and data limitations matter in research.

## 📄 Main project file

The complete executed notebook is in [`notebooks/Railway_Freight_Forecasting_Optimization.ipynb`](notebooks/Railway_Freight_Forecasting_Optimization.ipynb).

---

⭐ If you use or adapt this educational research prototype, please cite the ORR data source and clearly distinguish observed data from illustrative assumptions.

---

## 👤 Author

**Azmat Tanveer**  
🔗 [GitHub Profile](https://github.com/azmattanveer409)

---

> ⭐ If you found this project useful, please consider giving it a star on GitHub!

