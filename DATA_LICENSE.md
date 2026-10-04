# 🌱 Smart Irrigation ML System

A reproducible machine learning and simulated IoT project for intelligent irrigation decision support using soil moisture, temperature, and air-humidity data.

## Overview

Efficient irrigation is important for sustainable agriculture, water conservation, and climate-resilient food production.

This project investigates how environmental sensor data can support irrigation decisions using both simple rule-based methods and machine learning.

The project currently consists of two main stages:

- **V1 — Machine Learning Analysis and Validation**
- **V2A — Simulated IoT Irrigation Decision System**

V1 investigates whether machine learning provides meaningful improvement over a simple soil-moisture threshold.

V2A extends the analysis into a software-based IoT prototype that simulates incoming environmental sensor readings and generates real-time Pump ON/OFF recommendations.

> **Important:** V2A uses simulated sensor measurements. No physical IoT sensors, microcontrollers, relays, or pumps have yet been deployed.

---

## Research Questions

This project investigates the following questions:

1. Can machine learning predict irrigation pump status from environmental sensor measurements?
2. Does machine learning provide meaningful improvement over a simple soil-moisture threshold?
3. How stable are the models across different subsets of the dataset?
4. How much predictive information is contributed by Temperature and Air Humidity beyond Soil Moisture?
5. Can the validated decision logic be extended into a simulated real-time IoT irrigation system?
6. How do threshold-based and machine-learning irrigation decisions behave near the decision boundary?

---

# V1 — Machine Learning Analysis

## Dataset

The dataset contains **3,000 sensor observations** with four variables:

| Variable | Description |
|---|---|
| Soil Moisture | Raw soil-moisture sensor reading |
| Temperature | Air temperature |
| Air Humidity | Relative air humidity |
| Pump Data | Irrigation pump status: 0 = OFF, 1 = ON |

The dataset contains no missing values or duplicate observations.

### Dataset Source

The data were originally published by **Amritpal Kaur, Devershi Pallavi Bhatt, and Linesh Raja** and are associated with research on IoT-based smart irrigation.

**Dataset DOI:**  
https://doi.org/10.17632/fpdwmm7nrb.1

**Associated publication:**  
Kaur, A., Bhatt, D. P., & Raja, L. (2024). *Developing a Hybrid Irrigation System for Smart Agriculture Using IoT Sensors and Machine Learning in Sri Ganganagar, Rajasthan*. Journal of Sensors, 2024, Article 6676907.

https://doi.org/10.1155/2024/6676907

> This repository uses the published dataset for independent educational and research-oriented machine learning analysis. The original data collection was performed by the dataset authors.

---

## Methodology

The V1 workflow is:

```text
Sensor Dataset
      │
      ▼
Data Validation
      │
      ▼
Exploratory Data Analysis
      │
      ▼
Data Preprocessing
      │
      ▼
Threshold Baseline
      │
      ▼
Machine Learning
      │
      ▼
Model Evaluation
      │
      ▼
Robustness Validation
```

Three machine learning classifiers were evaluated:

- Logistic Regression
- Decision Tree
- Random Forest

A simple Soil Moisture threshold was also established as a baseline.

The dataset was initially divided into:

- **80% training data:** 2,400 observations
- **20% testing data:** 600 observations

Stratified sampling was used to preserve the Pump ON/OFF class distribution.

Additional robustness testing was performed using **5-fold stratified cross-validation**.

---

## Exploratory Data Analysis

The dataset contains:

- **1,569 Pump ON** observations
- **1,431 Pump OFF** observations
- No missing values
- No duplicate rows

Soil Moisture showed a strong negative correlation with Pump Data:

**Correlation ≈ -0.855**

Temperature and Air Humidity showed almost no linear correlation with pump status.

Average raw Soil Moisture readings were approximately:

- **Pump ON:** 509
- **Pump OFF:** 831

These observations suggested that Soil Moisture was likely to dominate pump prediction.

---

## Baseline Model

Before training machine learning models, a simple Soil Moisture threshold was optimized using the training data only.

**Optimal threshold ≈ 682.58**

The baseline achieved:

- **Training Accuracy:** 99.88%
- **Test Accuracy:** 99.83%

This provided a strong benchmark for evaluating whether more complex machine learning models offered meaningful improvement.

---

## Machine Learning Results

| Model | Accuracy | Precision | Recall | F1 Score |
|---|---:|---:|---:|---:|
| Soil Moisture Threshold | 99.83% | — | — | — |
| Logistic Regression | 99.83% | 99.68% | 100.00% | 99.84% |
| Decision Tree | 99.83% | 100.00% | 99.68% | 99.84% |
| **Random Forest** | **100.00%** | **100.00%** | **100.00%** | **100.00%** |

Random Forest correctly classified all **600 observations in the original test split**.

This result was investigated further rather than being interpreted as evidence that Random Forest would necessarily achieve perfect performance in real-world deployment.

---

## Feature Importance

Random Forest feature importance was:

| Feature | Importance |
|---|---:|
| **Soil Moisture** | **97.80%** |
| Air Humidity | 1.11% |
| Temperature | 1.09% |

The results show that Soil Moisture contains most of the predictive information available to the model.

---

# Model Validation and Robustness

Because the initial models achieved unusually high predictive performance, additional validation was performed to understand why.

## Threshold Analysis

The relationship between Soil Moisture and Pump Data revealed a very strong decision boundary.

- Pump ON Soil Moisture range: approximately **314.51–681.69**
- Pump OFF Soil Moisture range: approximately **395.47–984.83**
- Using the approximate threshold of **682.58**, only **4 of 3,000 observations** did not follow the threshold rule.
- No Pump ON observations occurred at or above the threshold.

Two of the four exceptions occurred very close to the threshold, while two occurred substantially below it.

This indicates that pump status in this dataset is almost completely determined by the raw Soil Moisture measurement.

---

## 5-Fold Cross-Validation

To determine whether the high model performance resulted from one favorable train/test split, **5-fold stratified cross-validation** was performed.

| Model | Mean Accuracy | Standard Deviation |
|---|---:|---:|
| Logistic Regression | 99.80% | 0.16% |
| Decision Tree | 99.80% | 0.19% |
| **Random Forest** | **99.93%** | **0.08%** |

All three models maintained very high performance across the five folds.

Random Forest achieved the highest mean accuracy and the lowest variation between folds.

---

## Single-Feature vs Multi-Feature Analysis

Random Forest was evaluated using Soil Moisture alone and then using all three environmental variables.

| Feature Set | Mean Accuracy | Standard Deviation | Minimum Accuracy | Maximum Accuracy |
|---|---:|---:|---:|---:|
| Soil Moisture Only | 99.77% | 0.17% | 99.50% | 100.00% |
| **All Features** | **99.93%** | **0.08%** | **99.83%** | **100.00%** |

Adding Temperature and Air Humidity improved mean accuracy by approximately **0.16 percentage points**.

This confirms that Soil Moisture alone explains the vast majority of pump-status predictions in the current dataset.

---

## V1 Key Findings

The experiments demonstrate that irrigation pump status in this dataset is highly predictable.

However, the validation analysis shows that the high accuracy must be interpreted carefully:

- Soil Moisture accounts for approximately **97.8%** of Random Forest feature importance.
- A simple Soil Moisture threshold achieves **99.83% test accuracy**.
- Only **4 of 3,000 observations** violate the approximate threshold rule.
- Random Forest achieves approximately **99.93% mean accuracy** under 5-fold cross-validation.
- Random Forest using Soil Moisture alone achieves approximately **99.77% mean cross-validation accuracy**.
- Adding Temperature and Air Humidity improves mean accuracy by only approximately **0.16 percentage points**.

Therefore, the results do **not** demonstrate that a complex machine learning model is necessary for this particular dataset.

For a simple embedded irrigation controller, a calibrated Soil Moisture threshold may provide a more interpretable and computationally efficient solution.

---

# V2A — IoT Sensor Simulation

V2A extends the project from offline machine learning analysis toward a **software-based IoT irrigation decision prototype**.

The objective is to simulate incoming environmental sensor measurements and process them as if they were being received continuously from an IoT monitoring system.

No physical hardware is used in V2A.

---

## Simulated IoT Architecture

```text
Simulated Environmental Sensors
             │
             ▼
     Soil Moisture
      Temperature
      Air Humidity
             │
             ▼
      Sensor Data Stream
             │
      ┌──────┴──────┐
      ▼             ▼
Soil-Moisture    Random Forest
  Threshold          Model
 Controller
      │             │
      └──────┬──────┘
             ▼
      Decision Comparison
             │
             ▼
      PUMP ON / PUMP OFF
             │
             ▼
     Monitoring & Logging
```

---

## Simulated Sensor Stream

The V2A prototype generates simulated measurements for:

- Soil Moisture
- Temperature
- Air Humidity
- Timestamp

Each incoming observation is processed to produce an irrigation recommendation.

The prototype also maintains a historical sensor log and visualizes environmental measurements over time.

This demonstrates the software workflow:

**Sensor Reading → Processing → Irrigation Decision → Monitoring**

---

## Threshold vs Random Forest Controller

Two irrigation decision approaches were implemented:

### 1. Threshold Controller

Uses the approximate Soil Moisture threshold identified during V1:

**Threshold ≈ 682.58**

For the current dataset scale:

- Below threshold → **PUMP ON**
- At or above threshold → **PUMP OFF**

### 2. Random Forest Controller

Uses:

- Soil Moisture
- Temperature
- Air Humidity

to predict:

- **0 = PUMP OFF**
- **1 = PUMP ON**

---

## Initial Simulated Stream Comparison

An initial stream of **10 simulated sensor observations** was generated.

The threshold controller and Random Forest produced identical irrigation recommendations for all 10 observations.

**Agreement: 100% (10/10)**  
**Disagreements: 0**

Because this was a small simulated sample and most observations were not close to the decision boundary, additional stress testing was performed.

---

## Decision-Boundary Stress Test

Fifteen Soil Moisture values were deliberately evaluated around the approximate **682.58** threshold.

The threshold controller and Random Forest disagreed on **3 of the 15 test conditions**:

| Soil Moisture | Threshold Controller | Random Forest |
|---:|---|---|
| 682.0 | PUMP ON | PUMP OFF |
| 682.3 | PUMP ON | PUMP OFF |
| 682.5 | PUMP ON | PUMP OFF |

This demonstrates that the threshold controller and Random Forest behave similarly but are **not identical near the learned decision boundary**.

The disagreement should not automatically be interpreted as evidence that Random Forest learned a superior physical irrigation rule. It reflects the decision structure learned from the available dataset.

---

## Temperature and Humidity Sensitivity Test

To investigate whether Temperature or Air Humidity influenced the Random Forest decision near the disagreement region, Soil Moisture was fixed at:

**682.3**

The following conditions were tested:

- Temperature: **20°C, 25°C, 30°C, 35°C, 38°C**
- Air Humidity: **40%, 50%, 60%, 70%, 80%**

This produced **25 Temperature/Humidity combinations**.

Random Forest predicted:

**PUMP OFF for all 25 combinations**

Within the tested ranges, changing Temperature and Air Humidity did not alter the Random Forest decision at Soil Moisture = 682.3.

This provides additional evidence that Soil Moisture dominates the model's decision behavior in the current dataset.

---

## V2A Key Findings

The simulated IoT prototype demonstrates how the machine learning analysis can be extended toward a real-time irrigation decision architecture.

The main findings are:

- Simulated environmental readings can be processed continuously.
- Sensor observations can be timestamped and stored as a monitoring history.
- Threshold and Random Forest decisions can be generated for each incoming observation.
- Both approaches produced identical decisions for the initial 10-reading simulation.
- Differences appeared when deliberately testing values close to the decision boundary.
- Temperature and Air Humidity did not change the Random Forest decision in the tested boundary sensitivity experiment.
- Soil Moisture remains the dominant decision variable.

V2A is therefore a **software prototype**, not a deployed IoT irrigation system.

---

# Project Evolution

The project currently follows this development path:

```text
V1
Machine Learning Analysis
        │
        ▼
Model Validation
        │
        ▼
V2A
Simulated IoT Monitoring
        │
        ▼
Future V2B
Physical IoT Sensors
        │
        ▼
V3
Weather Integration
        │
        ▼
V4
Remote Sensing
        │
        ▼
Future
Multimodal Agricultural AI
```

---

# Notebooks

## 01 — Data Exploration

Explores dataset structure, missing values, distributions, correlations, pump-status relationships, and environmental variables.

## 02 — Data Preprocessing

Validates the dataset, investigates potential outliers, defines features and target variables, performs stratified train/test splitting, establishes the threshold baseline, and scales features where required.

## 03 — Model Training and Comparison

Trains Logistic Regression, Decision Tree, and Random Forest classifiers and evaluates their performance using accuracy, precision, recall, F1-score, confusion matrices, and feature importance.

## 04 — Model Validation and Robustness

Investigates the unusually high predictive performance through threshold analysis, 5-fold stratified cross-validation, and single-feature versus multi-feature experiments.

## 05 — IoT Sensor Simulation

Extends the project toward an IoT-based irrigation decision system using simulated real-time Soil Moisture, Temperature, and Air Humidity measurements.

The notebook implements:

- Simulated sensor streaming
- Timestamps
- Sensor-history logging
- Time-series visualization
- Threshold-based irrigation decisions
- Random Forest predictions
- Threshold-versus-ML comparison
- Decision-boundary stress testing
- Temperature/Humidity sensitivity analysis

No physical IoT hardware is used in this stage.

---

# Technologies

- Python
- Pandas
- NumPy
- Matplotlib
- Scikit-learn
- Jupyter Notebook
- Google Colab
- GitHub

---

# Limitations

The current project has several important limitations:

- The original dataset contains only three predictor variables.
- Pump status is overwhelmingly associated with Soil Moisture.
- The dataset contains a very strong single-feature decision boundary.
- The machine learning models have not yet been validated using an independent field dataset.
- V2A uses randomly simulated sensor measurements rather than measurements from physical sensors.
- The simulation does not reproduce the full temporal and physical behavior of an agricultural field.
- The current system predicts Pump ON/OFF rather than irrigation water quantity.
- Weather forecasts and rainfall are not yet incorporated.
- Crop type and soil characteristics are not represented.
- Satellite or remote-sensing observations are not yet incorporated.
- No physical pump, relay, microcontroller, or sensor has been controlled by the system.

High predictive performance on the current dataset should therefore **not** be interpreted as evidence of equivalent performance in real agricultural environments.

---

# Future Work

The project will be progressively extended toward a more realistic smart-agriculture monitoring and decision-support system.

1. **Physical IoT implementation** — replace simulated measurements with data from real soil-moisture, temperature, and humidity sensors connected to a microcontroller such as an ESP32.

2. **Weather integration** — incorporate rainfall, temperature forecasts, humidity, and other meteorological information into irrigation decisions.

3. **Crop- and soil-specific modelling** — account for differences in crop water requirements and soil characteristics.

4. **Water-demand prediction** — move beyond binary Pump ON/OFF classification toward estimating required irrigation quantity.

5. **Temporal modelling** — analyze changes in environmental conditions and irrigation requirements over time.

6. **Remote sensing integration** — incorporate satellite observations and vegetation indices such as NDVI.

7. **Multimodal AI** — combine ground-sensor information, weather data, and satellite observations within a unified predictive framework.

8. **Field validation** — evaluate the system using independent real-world agricultural sensor and environmental data.

---

# Repository Structure

```text
Smart-Irrigation-ML/
│
├── data/
│   └── raw/
│       └── smart_irrigation_data.csv
│
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   ├── 02_data_preprocessing.ipynb
│   ├── 03_model_training.ipynb
│   ├── 04_model_validation.ipynb
│   └── 05_iot_simulation.ipynb
│
├── .gitignore
├── DATA_LICENSE.md
├── LICENSE
├── README.md
└── requirements.txt
```

---

# Reproducibility

Clone the repository:

```bash
git clone https://github.com/Innocent-I/Smart-Irrigation-ML.git
cd Smart-Irrigation-ML
```

Install the required Python packages:

```bash
pip install -r requirements.txt
```

The notebooks can then be executed sequentially from:

```text
01_data_exploration.ipynb
        ↓
02_data_preprocessing.ipynb
        ↓
03_model_training.ipynb
        ↓
04_model_validation.ipynb
        ↓
05_iot_simulation.ipynb
```

Google Colab can also be used to execute the notebooks without configuring a local Python environment.

---

# License

The original code, notebooks, and analysis developed for this repository are provided under the **MIT License**.

The dataset is third-party material and is **not covered by the repository's MIT License**.

See [`DATA_LICENSE.md`](DATA_LICENSE.md) for dataset attribution and licensing information.

---

# Author

**Innocent Irankunda**

Assistant Lecturer and ICT professional with interests in:

- Machine Learning
- Artificial Intelligence
- Internet of Things
- Data Analytics
- Remote Sensing
- Environmental Monitoring
- AI applications in agriculture

---

# Acknowledgment

The original irrigation sensor dataset was created and published by **Amritpal Kaur, Devershi Pallavi Bhatt, and Linesh Raja**.

This repository presents an independent analysis, validation, and software-based irrigation decision implementation using their publicly available dataset.

**Original Dataset:**  
https://doi.org/10.17632/fpdwmm7nrb.1

**Associated Publication:**  
https://doi.org/10.1155/2024/6676907
