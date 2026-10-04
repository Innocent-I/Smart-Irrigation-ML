# 🌱 Smart Irrigation ML System

A reproducible machine learning project for predicting irrigation pump status using soil moisture, air temperature, and humidity sensor data.

## Overview

Efficient irrigation is important for sustainable agriculture and water-resource management. This project investigates whether machine learning can predict irrigation pump decisions from environmental sensor measurements.

The project compares a simple soil-moisture threshold with multiple machine learning classifiers. The objective is not only to maximize prediction accuracy, but also to determine whether the additional complexity of machine learning is justified by the available data.

## Research Question

**Can machine learning improve irrigation pump prediction compared with a simple soil-moisture threshold?**

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

## Methodology

The project follows the workflow:

**Sensor Data → Data Validation → Exploratory Data Analysis → Preprocessing → Baseline Model → Machine Learning → Model Evaluation**

Three machine learning classifiers were evaluated:

- Logistic Regression
- Decision Tree
- Random Forest

A simple soil-moisture threshold was also established as a baseline.

The dataset was divided into:

- **80% training data:** 2,400 observations
- **20% testing data:** 600 observations

Stratified sampling was used to preserve the Pump ON/OFF class distribution.

## Exploratory Data Analysis

The dataset contains:

- 1,569 Pump ON observations
- 1,431 Pump OFF observations
- No missing values
- No duplicate rows

Soil Moisture showed a strong negative correlation with Pump Data:

**Correlation ≈ -0.855**

Temperature and Air Humidity showed almost no linear correlation with pump status.

Average raw Soil Moisture readings were approximately:

- **Pump ON:** 509
- **Pump OFF:** 831

These observations suggested that soil moisture was likely to dominate pump prediction.

## Baseline Model

Before training machine learning models, a simple soil-moisture threshold was optimized using the training data only.

**Optimal threshold ≈ 682.58**

The baseline achieved:

- **Training Accuracy:** 99.88%
- **Test Accuracy:** 99.83%

This provided a strong benchmark for evaluating whether more complex machine learning models offered meaningful improvement.

## Machine Learning Results

| Model | Accuracy | Precision | Recall | F1 Score |
|---|---:|---:|---:|---:|
| Soil Moisture Threshold | 99.83% | — | — | — |
| Logistic Regression | 99.83% | 99.68% | 100.00% | 99.84% |
| Decision Tree | 99.83% | 100.00% | 99.68% | 99.84% |
| **Random Forest** | **100.00%** | **100.00%** | **100.00%** | **100.00%** |

Random Forest correctly classified all **600 test observations** in this particular train/test split.

## Feature Importance

Random Forest feature importance:

| Feature | Importance |
|---|---:|
| **Soil Moisture** | **97.80%** |
| Air Humidity | 1.11% |
| Temperature | 1.09% |

The results confirm that the raw soil-moisture measurement contains most of the predictive information in this dataset.

## Key Finding

Although Random Forest achieved 100% test accuracy, the simple soil-moisture threshold already achieved **99.83%**.

Therefore, this experiment does **not** demonstrate that a complex machine learning model is necessary for this particular irrigation dataset.

For a simple embedded irrigation controller, a calibrated soil-moisture threshold may provide a more interpretable and computationally efficient solution.

Machine learning may provide greater value when additional variables and more complex real-world conditions are introduced.

## Repository Structure

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
│   └── 03_model_training.ipynb
│
└── README.md

Notebooks
01 — Data Exploration
Explores dataset structure, missing values, distributions, correlations, pump-status relationships, and environmental variables.
02 — Data Preprocessing
Validates the dataset, investigates potential outliers, defines features and target variables, performs stratified train/test splitting, establishes the threshold baseline, and scales features where required.
03 — Model Training and Comparison
Trains Logistic Regression, Decision Tree, and Random Forest classifiers and evaluates their performance using accuracy, precision, recall, F1-score, confusion matrices, and feature importance.
Technologies
- Python
- Pandas
- NumPy
- Matplotlib
- Scikit-learn
- Google Colab
- GitHub
Limitations
- The dataset contains only three predictor variables.
- Pump status is overwhelmingly associated with soil moisture.
- Results are based on a single train/test split.
- The models have not yet been validated using an independent field dataset.
- High performance on this dataset should not be interpreted as equivalent performance under real agricultural conditions.
Future Work
The project will be progressively extended toward a more realistic smart-agriculture monitoring system through:
1. Real-time IoT integration — collect soil and environmental measurements directly from field sensors.
2. Weather integration — incorporate rainfall, temperature forecasts, and other meteorological variables.
3. Crop- and soil-specific modelling — account for different irrigation requirements.
4. Water-demand prediction — move beyond Pump ON/OFF classification to estimating irrigation quantity.
5. Remote sensing — integrate satellite observations and vegetation indices such as NDVI.
6. Multimodal AI — combine ground sensors, weather information, and satellite imagery for more robust agricultural decision support.
Author
Innocent Irankunda
Research interests: Machine Learning, Artificial Intelligence, IoT, Data Analytics, Remote Sensing, Environmental Monitoring, and AI applications in agriculture.
Acknowledgment
The original irrigation sensor dataset was created and published by Kaur, Bhatt, and Raja. This repository presents an independent analysis and machine learning implementation using their publicly available dataset.
