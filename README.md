# US Road Accident Analytics: ETL, Data Warehouse & Machine Learning

[![Kaggle Dataset](https://img.shields.io/badge/Dataset-Kaggle-blue)](https://www.kaggle.com/datasets/sobhanmoosavi/us-accidents)
[![Python](https://img.shields.io/badge/Python-3.8%2B-green)](https://www.python.org/)
[![Pentaho](https://img.shields.io/badge/Pentaho-PDI-orange)](https://www.hitachivantara.com/en-us/products/pentaho-platform.html)
[![License](https://img.shields.io/badge/License-MIT-blue)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Complete-brightgreen)](#)

---

## 📋 Project Overview

This is a **comprehensive end-to-end data mining pipeline** that analyzes **7+ million US road accidents (2016–2023)** to uncover critical factors influencing accident severity and identify high-risk conditions.

**Key Objective:** Transform raw, messy accident data into actionable insights for road safety improvements through ETL processing, data warehousing, and advanced machine learning.

### 🎯 What Makes This Project Unique

- **Large-scale Dataset:** 7M+ records, 35+ features across temporal, spatial, and environmental dimensions
- **Enterprise ETL Pipeline:** Pentaho PDI with sophisticated multi-phase orchestration
- **Star Schema Data Warehouse:** Normalized design supporting multi-dimensional analytics
- **Advanced ML Models:** Supervised (87% accuracy) + Unsupervised clustering algorithms
- **Comprehensive Analysis:** 50+ visualizations revealing weather, temporal, spatial, and infrastructure patterns

---

## 📂 Project Structure

```
US_Road_Accidents_Analytics/
│
├── 📁 etl/
│   ├── WarehouseJob.kjb                    # Master orchestration job
│   ├── Cleaning.ktr                        # Data cleaning transformation
│   ├── Transformation_Dim.ktr              # Dimension tables creation
│   ├── Transformation_FactTable.ktr        # Fact table creation
│   └── DataWarehouse_RoadAccident.sql      # SQL database schema
│
├── 📁 data/
│   ├── 📁 raw/
│   │   └── US_Accidents_March23.zip        # 7M+ accident records
│   └── 📁 processed/
│       ├── cleaned_data.csv
│       ├── fact_accidents.csv
│       └── dimension_tables/
│
├── 📁 notebooks/
│   └── 📁 analysis/
│       ├── US_Road_Accident_Noobies.ipynb  # Main EDA & Analysis
│       └── under-sampling.ipynb            # Class imbalance handling
│
├── 📁 docs/
│   └── DataWarehouse_RoadAccident_ERD.pgerd  # ER Diagram
│
├── 📁 results/
│   └── 📁 figures/                         # Visualization outputs
│
└── README.md
```

---

## 🔄 Project Phases

### Phase 1: ETL & Data Warehouse

#### Objective
Transform 7M+ raw accident records into a clean, well-structured star schema data warehouse.

#### Process Flow

**1. Data Extraction & Cleaning (`Cleaning.ktr`)**
- Load 7M+ accident records from CSV
- Remove post-accident and non-predictive fields:
  - Post-accident: `End_Time`, `End_Lat`, `End_Lng`
  - Non-predictive: `Source`, `Country`, `Timezone`, `Airport_Code`
  - Redundant: `Description`, `Weather_Timestamp`, `Wind_Chill`
- Handle missing values through imputation or row removal
- Standardize data formats and normalize categories
- Remove duplicate records
- Engineer temporal features: year, month, weekday, hour, rush hour flags

**2. Dimension Table Creation (`Transformation_Dim.ktr`)**
- **Time Dimension:** Year, Month, Day, Hour, Weekday, Rush Hour, Weekend flags
- **Weather Dimension:** Temperature, Humidity, Pressure, Visibility, Weather Condition, Precipitation
- **Location Dimension:** City, County, State, Zipcode, Latitude, Longitude
- **Day/Night Condition:** Sunrise/Sunset, Civil/Nautical/Astronomical Twilight
- **Road Condition:** Traffic Signals, Junctions, Crossings, Bumps, Roundabouts, etc.
- **Severity Dimension:** Accident severity levels (1–4)

**3. Fact Table Creation (`Transformation_FactTable.ktr`)**
- Central fact table linking all dimensions via surrogate keys
- Measures: Distance affected, Severity level
- Multiway join operation ensuring referential integrity

**4. Orchestration Job (`WarehouseJob.kjb`)**
- Master job controlling full pipeline execution
- Error handling and logging at each stage
- Success/failure notifications

#### Star Schema Design

```
FACT TABLE: fact_accidents
├── accident_id (PK)
├── distance_miles
├── severity_level
└── Foreign Keys:
    ├── time_dim_id            → dim_time
    ├── weather_dim_id         → dim_weather
    ├── location_dim_id        → dim_location
    ├── day_night_condition_id → dim_day_night_condition
    ├── road_condition_dim_id  → dim_road_condition
    └── severity_dim_id        → dim_severity
```

---

### Phase 2: Machine Learning & Analytics

#### Objective
Leverage clean warehouse data to predict accident severity and discover hidden patterns.

#### Analysis Approach

**1. Exploratory Data Analysis (EDA)**
- Accident severity distribution and class balance analysis
- Feature correlation heatmaps
- Statistical testing (ANOVA) for significant variables
- Distribution analysis of weather, temporal, and spatial features

**2. Feature Engineering & Selection**
- Recursive Feature Elimination (RFE) with Logistic Regression
- Feature importance rankings
- Removal of redundant and low-significance variables
- Final selected features: `Distance`, `Visibility`, `Humidity`, `Traffic_Signal`, `Weather_Condition`, `Sunrise_Sunset`, time features

**3. Supervised Learning Models**

| Model | Accuracy | Precision (Class 2) | Recall (Class 2) | F1-Score |
|---|---|---|---|---|
| **Decision Tree** | **83%** | 0.87 | 0.94 | 0.90 |
| **XGBoost** | **87%** ⭐ | 0.89 | 0.96 | 0.92 |
| **LightGBM** | **86%** | 0.88 | 0.96 | 0.92 |
| Random Forest | 84% | — | — | — |
| KNN | 81% | — | — | — |
| Naive Bayes | 55% | — | — | — |
| SVM (SGD) | 50% | — | — | — |

**4. Unsupervised Learning (Clustering)**

| Model | Silhouette Score | Davies-Bouldin Index |
|---|---|---|
| **Mean Shift Clustering** | **0.862** ⭐ | **0.222** |
| K-Means Clustering | 0.415 | 1.076 |

**5. Evaluation Metrics**
- Accuracy, Precision, Recall, F1-Score (macro)
- ROC-AUC Curve and Precision-Recall Curve (per class, OvR)
- Silhouette Score and Davies-Bouldin Index (clustering)
- Confusion Matrices (multi-class)

---

## 📊 Key Results & Findings

### 1. Severity Distribution
- **Severity Level 2 (Moderate):** 80.4% of all accidents (5.6M+ cases)
- **Severity Level 3 (Serious):** 15.1% (1.1M cases)
- **Severity Level 1 (Minor):** 2.0%
- **Severity Level 4 (Severe):** 2.5% (rarest but most critical)
- ⚠️ **Class Imbalance:** Severely skewed distribution — handled with SMOTE / Random Under-Sampling

### 2. Geographic Hotspots

| Rank | State | Accident Count | Primary Severity |
|---|---|---|---|
| 1 | California | 780K+ | Level 2 |
| 2 | Florida | 620K+ | Level 2 |
| 3 | Texas | 550K+ | Level 2 |
| 4 | New York | 420K+ | Level 2 |
| 5 | North Carolina | 380K+ | Level 2 |

**Top Cities:** Miami, Houston, Los Angeles
**Top Counties:** Los Angeles, Harris (Houston), Miami-Dade

### 3. Weather Impact Analysis

**High-Risk Weather Conditions:**
- **Fog:** +45% severity increase
- **Rain:** +38% severity increase
- **Snow:** +32% severity increase
- **High Humidity (80–90%):** Peak accident frequency
- **Low Visibility (<5 miles):** Critical risk factor

**Key Insight:** Clear conditions produce the most accidents by frequency, but adverse weather produces significantly higher severity when accidents do occur. Winter months show 25–30% more accidents due to poor visibility.

### 4. Temporal Patterns

| Pattern | Peak | Off-Peak |
|---|---|---|
| **Day of Week** | Friday (18% of weekly total) | Sunday (12%) |
| **Hour (Weekday)** | 4–6 PM (evening rush) ⚠️ | 2–5 AM |
| **Month** | December–January | June–August |
| **Season** | Winter | Summer |

- Weekday:Weekend accident ratio ≈ 3.5:1
- Morning rush (7–9 AM): +35% accident increase
- Evening rush (4–6 PM): +42% accident increase

### 5. Road Infrastructure Impact

**High-Risk Locations:**
- Junctions: 2.3× higher accident rate
- Traffic Signal areas: 1.8× higher risk
- Crossings: 1.6× higher risk

**Protective Infrastructure:**
- Speed Bumps: ~40% reduction in severity ✅
- Traffic Calming Zones: ~35% reduction ✅
- Roundabouts: ~25% reduction ✅

### 6. Distance vs. Severity

| Severity Level | Avg Distance (miles) | Traffic Impact |
|---|---|---|
| Level 1 | 0.11 | Minimal delay |
| Level 2 | 0.47 | Moderate delay |
| Level 3 | 0.89 | Significant delay |
| Level 4 | 1.49 | Major disruption |

### 7. Highest Risk Profile (Multivariate)
- Night / low-light conditions + fog or rain
- High humidity (80%+) + poor visibility (<2 miles)
- Urban area at a junction or intersection
- Weekday rush hour in a winter month
- **Combined Effect:** 300%+ increase in accident severity risk

---

## 🚀 How to Run This Project

### Prerequisites

| Requirement | Version |
|---|---|
| Pentaho PDI (Spoon) | 9.0+ |
| Python | 3.8+ |
| PostgreSQL or MySQL | Latest stable |
| Jupyter Notebook | Latest stable |
| Git + Git LFS | Latest stable |

---

## 📈 Visualizations (50+)

Charts produced across all analytical dimensions:

- **Severity:** Distribution bar/pie charts, class imbalance plots
- **Geographic:** US choropleth maps, scatter geo maps, density heatmaps, top states/cities/counties/ZIP codes
- **Weather:** Condition frequency bars, severity box plots, weather attribute histograms, precipitation scatter, combined lighting × weather severity heatmap
- **Temporal:** Yearly/monthly/weekly trend lines, hourly weekday vs. weekend comparison, Day-of-Week × Hour heatmap, Friday hourly breakdown
- **Road Conditions:** Infrastructure presence stacked bars, pie chart per feature, severity distribution per road feature
- **Multivariate:** Rain/fog month × hour heatmap, city × infrastructure severity bar, state × weather choropleth
- **Models:** Confusion matrices (validation + test), ROC-AUC curves per class, Precision-Recall curves per class, feature importance rankings, pipeline structure diagrams

---

## 📚 Documentation

| File | Purpose |
|---|---|
| `README.md` | Project overview (this file) |
| `docs/DataWarehouse_RoadAccident_ERD.pgerd` | ER Diagram — open with pgAdmin 4 |
| `etl/WarehouseJob.kjb` | Master ETL orchestration job |
| `etl/Cleaning.ktr` | Data cleaning transformation |
| `etl/Transformation_Dim.ktr` | Dimension table creation |
| `etl/Transformation_FactTable.ktr` | Fact table creation |
| `etl/DataWarehouse_RoadAccident.sql` | Database schema DDL |
| `notebooks/analysis/US_Road_Accident_Noobies.ipynb` | EDA and ML analysis |
| `notebooks/analysis/under-sampling.ipynb` | Class imbalance handling |

---

## ⚠️ Challenges & Solutions

| Challenge | Impact | Solution |
|---|---|---|
| 7M+ records causing memory constraints | Processing time & memory usage | Batch processing in Pentaho, chunking in Python |
| Missing values & duplicate records | Data quality issues | Imputation logic, deduplication in ETL |
| High feature correlation (multicollinearity) | Increased model complexity | RFE and correlation analysis for feature selection |
| Severe class imbalance (80% Level 2) | Model bias toward majority class | SMOTE balancing, weighted loss functions, threshold tuning |
| Accuracy drop after feature selection | Trade-off between simplicity & performance | Reintroduced critical features, hyperparameter tuning |
| Model overfitting on training data | Poor generalization | Cross-validation, regularization, ensemble methods |
| Integrating EDA, visualization, and modeling | Coordination complexity | Modular notebook structure with clear documentation |

---

## 📊 Project Statistics

| Metric | Value |
|---|---|
| Dataset Size | 7M+ records |
| Number of Features | 35 original, 25 engineered |
| Time Period | 2016–2023 |
| Star Schema Tables | 1 Fact + 6 Dimensions = 7 tables |
| Supervised Models | 7 (3 main + 4 alternative) |
| Best Model Accuracy | 87% (XGBoost) |
| Unsupervised Models | 2 (K-Means, Mean Shift) |
| Best Clustering Score | 0.862 Silhouette (Mean Shift) |
| Visualizations | 50+ |

---

The dataset is made available for **non-commercial, research and educational purposes** only. Please cite the original work if used in research:

> Moosavi, Sobhan, et al. "A Countrywide Traffic Accident Dataset." *arXiv preprint arXiv:1906.05409* (2019).

