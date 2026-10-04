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

**Sensor Data → Data Validation → Exploratory Data Analysis → Preprocessing → Baseline Model → Machine Learning → Model Evaluation → Robustness Validation**

Three machine learning classifiers were evaluated:

- Logistic Regression
- Decision Tree
- Random Forest

A simple soil-moisture threshold was also established as a baseline.

The dataset was divided into:

- **80% training data:** 2,400 observations
- **20% testing data:** 600 observations

Stratified sampling was used to preserve the Pump ON/OFF class distribution.

Additional validation was performed using **5-fold stratified cross-validation**.

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

## Baseline Model

Before training machine learning models, a simple Soil Moisture threshold was optimized using the training data only.

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

The results show that the raw Soil Moisture measurement contains most of the predictive information available to the model.

## Model Validation and Robustness

Because the initial models achieved unusually high predictive performance, additional validation was performed to determine whether the results depended on a favorable train/test split and to better understand the structure of the dataset.

### Threshold Analysis

The relationship between Soil Moisture and Pump Data revealed a very strong decision boundary.

- Pump ON Soil Moisture range: approximately **314.51–681.69**
- Pump OFF Soil Moisture range: approximately **395.47–984.83**
- Using the approximate threshold of **682.58**, only **4 of 3,000 observations** did not follow the threshold rule.
- No Pump ON observations occurred at or above the threshold.

Two of the four exceptions occurred very close to the threshold, while two occurred substantially below it.

This indicates that pump status in this dataset is almost completely determined by the raw Soil Moisture measurement.

### 5-Fold Cross-Validation

To evaluate model stability across different subsets of the data, **5-fold stratified cross-validation** was performed.

| Model | Mean Accuracy | Standard Deviation |
|---|---:|---:|
| Logistic Regression | 99.80% | 0.16% |
| Decision Tree | 99.80% | 0.19% |
| **Random Forest** | **99.93%** | **0.08%** |

All three models maintained very high performance across the five folds.

Random Forest achieved the highest mean accuracy and the lowest variation between folds. This indicates that the high predictive performance observed in the original experiment was not simply the result of one favorable train/test split.

## Single-Feature vs Multi-Feature Analysis

To determine whether Temperature and Air Humidity provide substantial additional predictive information, Random Forest was evaluated using Soil Moisture alone and using all three environmental features.

| Feature Set | Mean Accuracy | Standard Deviation | Minimum Accuracy | Maximum Accuracy |
|---|---:|---:|---:|---:|
| Soil Moisture Only | 99.77% | 0.17% | 99.50% | 100.00% |
| **All Features** | **99.93%** | **0.08%** | **99.83%** | **100.00%** |

Adding Temperature and Air Humidity improved mean accuracy by approximately **0.16 percentage points**.

This indicates that these variables provide a small additional predictive contribution, while Soil Moisture alone explains the vast majority of pump-status predictions.

## Key Findings

The experiments demonstrate that irrigation pump status in this dataset is highly predictable.

Random Forest achieved **100% accuracy on the original 600-observation test set** and approximately **99.93% mean accuracy under 5-fold stratified cross-validation**.

However, the validation analysis shows that these results should be interpreted carefully:

- Soil Moisture accounts for approximately **97.8%** of Random Forest feature importance.
- A simple Soil Moisture threshold already achieves **99.83% test accuracy**.
- Only **4 of 3,000 observations** violate the approximate threshold rule.
- Random Forest using Soil Moisture alone achieves approximately **99.77% mean cross-validation accuracy**.
- Adding Temperature and Air Humidity improves mean accuracy by only approximately **0.16 percentage points**.

Therefore, the results do **not** demonstrate that a complex machine learning model is necessary for this particular dataset.

For a simple embedded irrigation controller, a calibrated Soil Moisture threshold may provide a more interpretable and computationally efficient solution.

The value of machine learning is expected to become more significant when additional real-world information is incorporated, including rainfall, weather forecasts, crop characteristics, soil properties, temporal sensor measurements, and remote-sensing observations.

## Notebooks

### 01 — Data Exploration

Explores dataset structure, missing values, distributions, correlations, pump-status relationships, and environmental variables.

### 02 — Data Preprocessing

Validates the dataset, investigates potential outliers, defines features and target variables, performs stratified train/test splitting, establishes the threshold baseline, and scales features where required.

### 03 — Model Training and Comparison

Trains Logistic Regression, Decision Tree, and Random Forest classifiers and evaluates their performance using accuracy, precision, recall, F1-score, confusion matrices, and feature importance.

### 04 — Model Validation and Robustness

Investigates the unusually high model performance through threshold analysis, 5-fold stratified cross-validation, and single-feature versus multi-feature experiments.

This notebook evaluates whether model complexity is justified by the predictive information available in the dataset.

## Technologies

- Python
- Pandas
- NumPy
- Matplotlib
- Scikit-learn
- Jupyter Notebook
- Google Colab
- GitHub

## Limitations

- The dataset contains only three predictor variables.
- Pump status is overwhelmingly associated with Soil Moisture.
- The dataset contains a very strong single-feature decision boundary.
- The models have not yet been validated using an independent field dataset.
- The current analysis does not incorporate temporal sensor behavior.
- Weather, rainfall, crop type, soil characteristics, and irrigation quantity are not represented.
- High predictive performance on this dataset should not be interpreted as evidence of equivalent performance under real-world agricultural conditions.

## Future Work

The project will be progressively extended toward a more realistic smart-agriculture monitoring and decision-support system through:

1. **Real-time IoT integration** — collect soil and environmental measurements directly from field sensors.
2. **Weather integration** — incorporate rainfall, temperature forecasts, and other meteorological variables.
3. **Crop- and soil-specific modelling** — account for different irrigation requirements across crops and soil conditions.
4. **Water-demand prediction** — move beyond Pump ON/OFF classification toward estimating irrigation quantity.
5. **Temporal modelling** — analyze changes in sensor measurements and irrigation requirements over time.
6. **Remote sensing** — integrate satellite observations and vegetation indices such as NDVI.
7. **Multimodal AI** — combine ground sensors, weather information, and satellite imagery for more robust agricultural decision support.
8. **Field validation** — evaluate the system using independent real-world agricultural sensor data.

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
│   ├── 03_model_training.ipynb
│   └── 04_model_validation.ipynb
│
├── .gitignore
├── DATA_LICENSE.md
├── LICENSE
├── README.md
└── requirements.txt
```

## License

The original code, notebooks, and analysis developed for this repository are provided under the **MIT License**.

The dataset is third-party material and is **not covered by the repository's MIT License**. See [`DATA_LICENSE.md`](DATA_LICENSE.md) for dataset attribution and licensing information.

## Author

**Innocent Irankunda**

Assistant Lecturer and ICT professional with interests in Machine Learning, Artificial Intelligence, IoT, Data Analytics, Remote Sensing, Environmental Monitoring, and AI applications in agriculture.

## Acknowledgment

The original irrigation sensor dataset was created and published by **Amritpal Kaur, Devershi Pallavi Bhatt, and Linesh Raja**.

This repository presents an independent analysis and machine learning implementation using their publicly available dataset.

**Dataset:** https://doi.org/10.17632/fpdwmm7nrb.1
