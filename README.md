# Papua Wildfire Prediction 

**Satellite-based Machine Learning System for Wildfire Potential Classification.**

This project utilizes **Google Earth Engine (GEE)** to extract environmental features (Weather, Vegetation, Surface Temperature) and trains Machine Learning models to classify wildfire hotspots in the Papua region.

| Remote Sensing | Data Engineering | Machine Learning |
| :---: | :---: | :---: |

---

### Tech Stack

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Google Earth Engine](https://img.shields.io/badge/Google_Earth_Engine-4285F4?style=for-the-badge&logo=google-earth&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![Scikit-Learn](https://img.shields.io/badge/Scikit_Learn-F7931E?style=for-the-badge&logo=scikit-learn&logoColor=white)
![XGBoost](https://img.shields.io/badge/XGBoost-EB4C42?style=for-the-badge&logo=xgboost&logoColor=white)

---

### Dataset & Features

Data is acquired dynamically using the Google Earth Engine API. The workflow involves extracting temporal data (e.g., rainfall *before* the fire event) for known fire hotspots (label `1`) and random non-fire points (label `0`).

| Feature | Description | Data Source (GEE) |
| :--- | :--- | :--- |
| **Precipitation** | 7-day rainfall sum prior to event | `UCSB-CHG/CHIRPS/DAILY` |
| **NDVI** | Vegetation density (30-day mean) | `MODIS/061/MOD13Q1` |
| **LST** | Land Surface Temperature | `MODIS/061/MOD11A1` |
| **Elevation** | Topographic altitude | `USGS/SRTMGL1_003` |
| **Label** | Thermal Anomalies (Fire Points) | `FIRMS` (T21 Band) |

> *Note: Soil Organic Carbon (Peatland) data was initially explored but dropped during feature selection due to low correlation with the target variable in this specific region.*

---

### Methodology

#### 1. Temporal Data Extraction
Unlike static datasets, this project builds a dataset by looking backward in time relative to each data point:
* **For Fire Points:** Extract weather/vegetation data from $T_{-7}$ to $T_{0}$ (date of fire).
* **For Non-Fire Points:** Generate random coordinates and timestamps, then extract corresponding environmental data to balance the dataset.

#### 2. Model Training & Strategy
The primary goal is to maximize **Recall**, as failing to detect a fire (False Negative) is more critical than a false alarm (False Positive).

* **Baseline:** Random Forest Classifier.
* **Optimization:** RandomizedSearchCV for hyperparameter tuning.
* **High-Recall Strategy:** Implemented `class_weight` adjustments and XGBoost tuning to prioritize minority class detection.

#### 3. Evaluation
Models were evaluated using Accuracy, Precision, Recall, and F1-Score.

| Model | Strategy | Focus |
| :--- | :--- | :--- |
| **Random Forest** | Standard | General Accuracy |
| **Random Forest (Weighted)** | `class_weight={0:1, 1:5}` | **High Recall (Safety)** |
| **XGBoost** | `scale_pos_weight` | Gradient Boosting Performance |

---

### Feature Importance

Based on the Random Forest analysis, the most significant predictors for wildfires in this region are:
1.  **LST (Land Surface Temperature):** High surface heat is the strongest indicator.
2.  **NDVI (Vegetation):** Dry vegetation acts as fuel.
3.  **Precipitation:** Lack of rain in the preceding week.

---

### Usage

1.  **Clone the repository**
    ```bash
    git clone [https://github.com/harits-edu/papua-wildfire-prediction.git](https://github.com/harits-edu/papua-wildfire-prediction.git)
    cd papua-wildfire-prediction
    ```

2.  **Environment Setup**
    Ensure you have the Google Earth Engine API authenticated.
    ```bash
    earthengine authenticate
    pip install geemap pandas scikit-learn xgboost matplotlib seaborn
    ```

3.  **Run the Notebook**
    Open `wildfire_prediction.ipynb` to execute the data pipeline and training process.

---

### Author

**Haritsyam Anshari**
[<img src="https://img.shields.io/badge/LinkedIn-0077B5?style=flat&logo=linkedin&logoColor=white" />](https://www.linkedin.com/in/haritsyam-anshari/)
