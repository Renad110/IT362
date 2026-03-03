
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
## 1. Overview of Primary Data Analysis
Data Source: Merged dataset (initially hajj_data.csv merged with external sources).
Context: Real-time pilgrim activities, crowd metrics, and physiological responses during the Hajj season.

Analysis Performed:

Descriptive Statistical Summary: Generated mean, standard deviation, and quartiles for 17 numerical features including Movement_Speed, Sound_Level_dB, Queue_Time_minutes, and Satisfaction_Rating.

Distribution Analysis: Histograms with Kernel Density Estimation (KDE) were plotted for all numerical features to identify skewness and central tendencies.

Categorical Frequency Analysis: Statistical summaries for categorical columns like Activity_Type, Fatigue_Level, and Nationality were conducted to identify the most frequent occurrences (e.g., 'Sa’i' as the top activity, 'Youth' as the dominant age group).

Interesting Findings & Observations:

Crowd Density: After filtering for the Hajj season, the Crowd_Density was found to be consistently High across the entire subset, leading to its removal as a constant feature for predictive modeling.

Pilgrim Sentiment: The Satisfaction_Rating and Perceived_Safety_Rating showed a mean of approximately 2.9 out of 5, indicating moderate satisfaction levels with significant variance (Std Dev ~1.4).

Physiological Stress: Both Fatigue_Level and Stress_Level most frequently appeared in the High and Medium categories, respectively, correlating with the intense nature of the pilgrimage rituals.

## 2. Overview of Secondary Data Analysis
Data Source: Weather data (initially weather_data.csv integrated into the main frame).
Context: Atmospheric conditions and their impact on the pilgrim environment.

Analysis Performed:

Feature Unification: Comparative analysis between redundant temperature fields (Temperature_x and Temperature_y) was performed to select the most reliable source, leading to the unification of weather metrics.

Statistical Summary of Environment: Analysis of Temperature, Dew Point, Humidity, and Wind Speed to understand the environmental constraints.

Interesting Findings & Observations:

Heat Extremes: The maximum temperature recorded was 45°C, with an average of 37.5°C, highlighting the extreme heat stress pilgrims face.

Low Humidity: The most frequent humidity level was as low as 5%, confirming an arid environment.

Weather Consistency: The most frequent condition was "Fair" (occurring 1,177 times out of 1,212 entries), which suggests that while temperatures are high, visibility and general weather patterns remain stable.

## 3. Tools and Libraries Used
Pandas: For data manipulation, loading (pd.read_csv), and statistical summaries (df.describe()).

Matplotlib.pyplot: For creating the structural framework of the distribution plots.

Seaborn: Used for advanced visualization, specifically sns.histplot with kde=True to visualize data distribution curves.

Scikit-Learn (StandardScaler): Utilized for the Z-score normalization of features like Fatigue_Level and Stress_Level.

## 4. Anomalies & Challenges Overcome
Redundancy Anomaly: It was observed that several columns (e.g., Date_x, Date_y, weather_dt) contained identical or overlapping temporal information. Decision: These were dropped to reduce dimensionality from 48 to 33 columns, ensuring the model focuses only on unique identifiers.

Constant Feature Discovery: Features like Is_Hajj_Season and Precip. were found to have zero variance (all values identical). Decision: These were excluded from further EDA as they provided no discriminatory power for analysis.

Categorical Encoding: To perform correlation analysis during EDA, categorical tiers (Low, Medium, High) were mapped to numeric values (1, 2, 3) to allow for mathematical processing.ing the sense of safety are critical for improving satisfaction. The next phase will involve deeper modeling to predict satisfaction levels based on these primary environmental and operational inputs

Date: 1/3/2026 & 2/3/2026 Data Preprocessing & Feature Engineering


## Task:

Documents the data preprocessing, cleaning, feature transformation, and standardization steps performed on the merged Hajj–Weather dataset.

The objective is to prepare a structured, fully numeric, and model-ready dataset suitable for advanced analysis and predictive modeling.

---

## 1. Data Cleaning & Structural Refinement

After completing Phase 1 (Data Integration & Initial EDA), the dataset required structural optimization to improve analytical reliability.

### Actions Performed:

### 1.1 Duplicate Removal

```python
merged.drop_duplicates(inplace=True)
```

* Checked for duplicated rows.
* No critical duplication detected.
* Ensured data integrity before transformation.

---

### 1.2 Redundant Column Removal

The following columns were removed due to redundancy or lack of analytical value:

* Date_x
* Date_y
* Time
* weather_dt
* Month
* Day
* DayOfWeek
* Weather_Conditions
* Temperature_x

### Reason:

* Date-related columns duplicated timestamp information.
* Weather_Conditions was textual and replaced with structured numeric weather metrics.
* Temperature_x duplicated Temperature_y.
* Some temporal breakdown columns could be regenerated later if needed.

This reduced dataset dimensionality and improved model efficiency.

---

## 2. Data Type Conversion & Unit Cleaning

Several weather-related features were stored as object type due to measurement units embedded within values.

### 2.1 Dew Point Cleaning

```python
merged["Dew Point"] = merged["Dew Point"].str.replace("°F", "").astype(float)
```

* Removed unit symbol.
* Converted to float.

---

### 2.2 Wind Speed Cleaning

```python
merged["Wind Speed"] = merged["Wind Speed"].str.replace("mph", "")
merged["Wind Speed"] = merged["Wind Speed"].replace("CALM", 0)
merged["Wind Speed"] = pd.to_numeric(merged["Wind Speed"], errors="coerce")
```

* Removed text unit.
* Converted "CALM" to 0.
* Ensured numeric compatibility.

---

### 2.3 Pressure Cleaning

```python
merged["Pressure"] = merged["Pressure"].str.replace("in", "")
merged["Pressure"] = pd.to_numeric(merged["Pressure"], errors="coerce")
```

---

### 2.4 Humidity Cleaning

```python
merged["Humidity"] = merged["Humidity"].str.replace("%", "")
merged["Humidity"] = pd.to_numeric(merged["Humidity"], errors="coerce")
```

---

### Result:

All environmental variables successfully converted to numeric format.

---

## 3. Hajj Season Validation

The dataset was verified to contain only Hajj-season records:

```python
merged["Is_Hajj_Season"].unique()
```

Result:

* Only value present: 1
* Confirms correct seasonal filtering.

Since this feature had zero variance, it was later excluded from modeling.

---

## 4. Categorical Encoding

To enable statistical modeling and correlation analysis, ordinal categorical variables were encoded numerically.

### Example:

```python
mapping = {"Low": 1, "Medium": 2, "High": 3}
merged["Fatigue_Level"] = merged["Fatigue_Level"].map(mapping)
merged["Stress_Level"] = merged["Stress_Level"].map(mapping)
```

### Purpose:

* Preserve ordinal relationship.
* Enable mathematical operations.
* Prepare for feature scaling.

---

## 5. Feature Scaling (Standardization)

To ensure uniform feature magnitude and prevent dominance of large-scale variables, **Z-score normalization** was applied.

```python
from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()
merged[numerical_cols] = scaler.fit_transform(merged[numerical_cols])
```

### Features Standardized:

* Movement_Speed
* Queue_Time_minutes
* Waiting_Time_for_Transport
* Security_Checkpoint_Wait_Time
* Distance_Between_People_m
* Sound_Level_dB
* Satisfaction_Rating
* Perceived_Safety_Rating
* Weather-related numeric variables

### Outcome:

* Mean ≈ 0
* Standard Deviation ≈ 1
* Improved comparability across features

---

## 6. Dimensionality Optimization

After cleaning and removal of redundant variables:

* Columns reduced from 48 → 33
* All remaining features are numeric and structured
* Dataset is fully modeling-ready

---

## 7. Data Quality Verification

Post-cleaning validation checks were performed:

* No missing values detected
* No duplicated rows
* No object-type weather metrics remaining
* All categorical tiers successfully encoded

---

## Summary

Phase Two successfully transformed the merged Hajj–Weather dataset into a clean, structured, and standardized analytical dataset.

Key Achievements:

* Removed redundant temporal columns
* Cleaned unit-based weather variables
* Encoded ordinal categories
* Applied Z-score normalization
* Reduced dimensionality
* Ensured zero missing values

The dataset is now fully prepared for advanced modeling, predictive analytics, and correlation analysis in the next phase.

---

.


