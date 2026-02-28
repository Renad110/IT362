
# Logbook – Phase 2: Data Collection and Initial Integration
**Date:** 28 Feb 2026

---

## Task: documents the data collection process and the initial integration of the Hajj activity dataset with hourly weather data.  
The objective is to analyze how weather conditions influence crowd behavior, movement patterns, and overall pilgrim experience during Hajj 2024.

---

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

---

## Dataset 2 – KSA Weather Dataset (2020–2025)

- Source: Kaggle  
- URL: https://www.kaggle.com/datasets/ziadwael/ksa-weather-dataset-2020-2025  
- Date of Collection: 24 Feb 2026  
- Collection Method: Direct manual download from Kaggle  
- File Format: CSV  

## Main Features:

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

## Challenges:

- Covers multiple years (2020–2025).
- Date and Time stored separately.
- Required datetime transformation before merging.

---

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

---

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

---

### Step 3: Save Integrated Dataset

```python
merged.to_csv("merged_output.csv", index=False)
```

The final merged dataset was saved for further processing and analysis.

---

## Post-Merge Dataset Overview

## Shape

Rows: 1212  
Columns: 48  

The merge was successful and produced 1212 matched observations.

---

### Data Types Overview

- Timestamp and weather_dt stored as datetime64.
- Location coordinates stored as float64.
- Behavioral metrics stored as numeric types.
- Several weather-related features (Temperature_y, Humidity, Wind Speed, Wind Gust, Pressure, Precip.) are stored as object type and will require type conversion.

---

### Missing Values Analysis

A missing value check was performed.

Result:
- No missing values detected.
- All 1212 records are complete.

---

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

---

## Summary

- Both datasets were successfully collected from Kaggle.
- Weather datetime column was created for alignment.
- Time-based merging was performed using nearest timestamp logic.
- The final dataset contains 1212 records and 48 columns.
- No missing values were detected.
- Dataset is ready for Data Cleaning and Exploratory Data Analysis (EDA).


