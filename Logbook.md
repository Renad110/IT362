
# Logbook – Phase 2: Data Processing, Cleaning, and Exploratory Data Analysis (EDA)
**Date:** 28 Feb 2026

## Task:
documents the data collection process and the initial integration of the Hajj activity dataset with hourly weather data.  
The objective is to analyze how weather conditions influence crowd behavior, movement patterns, and overall pilgrim experience during Hajj 2024.


## Dataset 1 – Hajj and Umrah Crowd Management Dataset (2024)

- Source: Kaggle  
- URL: https://www.kaggle.com/datasets/yarayahyahassan/hajj-and-umrah-crowd-management-dataset-2024  
- Date of Collection: 6 Feb 2026  
- Collection Method: Direct manual download from Kaggle  
- File Format: CSV  

### Main Features:

Timestamp  
Location_Lat  
Location_Long  
Crowd_Density  
Movement_Speed  
Activity_Type  
Weather_Conditions  
Temperature  
Sound_Level_dB  
Fatigue_Level  
Stress_Level  
Queue_Time_minutes  
Health_Condition  
Age_Group  
Nationality  
Transport_Mode  
Emergency_Event  
Incident_Type  
Crowd_Morale  
Pilgrim_Experience  
Month  
Day  
Hour  
Is_Hajj_Season  

### Challenges:

- Dataset limited to Hajj 2024.
- Requires alignment with external weather data.
- Timestamp format needed compatibility with weather datetime.


## Dataset 2 – KSA Weather Dataset (2020–2025)

- Source: Kaggle  
- URL: https://www.kaggle.com/datasets/ziadwael/ksa-weather-dataset-2020-2025  
- Date of Collection: 24 Feb 2026  
- Collection Method: Direct manual download from Kaggle  
- File Format: CSV  

### Main Features:

Date  
Time  
Temperature  
Humidity  
Wind  
Wind Speed  
Wind Gust  
Pressure  
Precip.  
Condition  

### Challenges:

- Covers multiple years (2020–2025).
- Date and Time stored separately.
- Required datetime transformation before merging.


## Data Integration Process

To study the influence of weather on Hajj crowd behavior, the two datasets were merged based on time alignment.

### Step 1: Create Unified Datetime Column (Weather Data)

```python
weather_df["weather_dt"] = pd.to_datetime(
    weather_df["Date"].astype(str) + " " + weather_df["Time"].astype(str),
    errors="coerce"
)
```

This step combines Date and Time into a single datetime column (weather_dt).


### Step 2: Time-Based Merge Using Nearest Timestamp

```python
merged = pd.merge_asof(
    hajj_df.sort_values("Timestamp"),
    weather_df.sort_values("weather_dt"),
    left_on="Timestamp",
    right_on="weather_dt",
    direction="nearest"
)
```

merge_asof was used to match each Hajj record with the closest hourly weather observation.


### Step 3: Save Integrated Dataset

```python
merged.to_csv("merged_output.csv", index=False)
```

The final merged dataset was saved for further processing and analysis.


## Post-Merge Dataset Overview

### Shape

Rows: 1212  
Columns: 48  

The merge was successful and produced 1212 matched observations.


### Data Types Overview

- Timestamp and weather_dt stored as datetime64.
- Location coordinates stored as float64.
- Behavioral metrics stored as numeric types.
- Several weather-related features (Temperature_y, Humidity, Wind Speed, Wind Gust, Pressure, Precip.) are stored as object type and will require type conversion.


### Missing Values Analysis

A missing value check was performed.

Result:
- No missing values detected.
- All 1212 records are complete.


### Key Observations After Merge

1. Duplicate Temperature Columns:
   - Temperature_x (Hajj dataset)
   - Temperature_y (Weather dataset)

   These will need review during cleaning.

2. Duplicate Date Columns:
   - Date_x
   - Date_y

3. Weather variables currently stored as object type require conversion to numeric.

4. Time Coverage:
   - Data spans from 6 June 2024 to 18 July 2024.
   - Matches Hajj 2024 season.

5. Is_Hajj_Season column contains only value 1, confirming filtering to Hajj period.


## Summary

- Both datasets were successfully collected from Kaggle.
- Weather datetime column was created for alignment.
- Time-based merging was performed using nearest timestamp logic.
- The final dataset contains 1212 records and 48 columns.
- No missing values were detected.
- Dataset is ready for Data Cleaning and Exploratory Data Analysis (EDA).

---

## Exploratory Data Analysis (EDA)
## 1. Objective & Overview
The goal of this session was to perform a comprehensive EDA on the primary dataset (1,212 records, 48 features) to understand the factors influencing the pilgrim experience, specifically focusing on movement, safety, and environmental conditions.

## 2. Tools & Libraries Used
The analysis was performed using Python within a Jupyter Notebook environment. Key libraries included:

Pandas: For data loading, cleaning, and manipulation.

Matplotlib & Seaborn: For generating statistical visualizations and heatmaps.

NumPy: For numerical operations and correlation calculations.

## 3. Types of Analysis Performed

Data Profiling: Inspected data types and confirmed there were zero missing values, ensuring the high quality of the primary data.

Univariate Analysis: Analyzed the distribution of individual variables like Movement_Speed, Temperature, and Queue_Time_minutes using Histograms and Kernel Density Estimate (KDE) plots.

Bivariate & Multivariate Analysis: Explored relationships between multiple variables to identify how environmental factors affect pilgrim behavior.

Correlation Analysis: Generated a correlation matrix to quantify the strength of relationships between features.

## 4. Key Findings & Interpretations

Safety as a Driver: A significant positive correlation (~0.7) was found between Perceived_Safety_Rating and Satisfaction_Rating. This indicates that safety is the primary contributor to a positive pilgrim experience.

Operational Bottlenecks: Strong correlations exist between Queue_Time_minutes, Waiting_Time_for_Transport, and Security_Checkpoint_Wait_Time. This suggests that delays in one area often cascade into others.

Environmental Impact: A moderate negative correlation (~ -0.37) was observed between Movement_Speed and Sound_Level_dB. Higher noise levels are associated with slower movement, likely due to increased crowd density or disorientation.

## 5. Anomalies & Observations

Data Consistency: The Is_Hajj_Season column contained a constant value (1), confirming the data is focused exclusively on the peak pilgrimage period.

AR Engagement: The analysis showed a high frequency of 'Started' status in AR System Interactions, indicating successful initial engagement with digital navigation tools.

Temperature Variance: Observed differences between internal and external temperatures (Temperature_x and Temperature_y), which warrants further investigation into how indoor cooling affects movement patterns.

## 6. Conclusion & Next Steps
The EDA confirms that managing queue times and enhancing the sense of safety are critical for improving satisfaction. The next phase will involve deeper modeling to predict satisfaction levels based on these primary environmental and operational inputs
