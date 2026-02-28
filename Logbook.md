# Data Science Project – Phase 2  
## Logbook – Data Collection  
**Project Topic:** Hajj Crowd Analysis and Weather Impact  
**Date:** 28 February 2026  

---

# 1. Introduction

This logbook documents the data collection process for Phase 2 of the project, including dataset sources, download methods, and initial observations prior to processing and cleaning.

The project aims to analyze Hajj crowd data and integrate it with weather data based on time and date to study possible relationships between weather conditions and crowd density patterns.

---

# 2. Dataset 1 – KSA Weather Dataset (2020–2025)

- **Source:** Kaggle  
- **Dataset Name:** KSA Weather Dataset 2020–2025  
- **URL:** https://www.kaggle.com/datasets/ziadwael/ksa-weather-dataset-2020-2025  
- **Date of Collection:** 28 Feb 2026  
- **File Format:** CSV  
- **Time Coverage:** 2020–2025  
- **Geographic Scope:** Saudi Arabia (including Makkah if available)  
- **Collection Method:** Direct manual download from Kaggle platform  
- **Storage Location:** `/Raw_Data/weather_raw.csv`

### Initial Observations:

- Data appears to be daily weather observations.
- Likely includes columns such as:
  - Date
  - Temperature
  - Humidity
  - Wind Speed
  - Pressure
- Date format is expected to be in `YYYY-MM-DD`.
- Dataset spans multiple years (2020–2025), which is broader than the Hajj dataset.
- Possible presence of missing values (Null entries).
- Data granularity: Daily level.

---

# 3. Dataset 2 – Hajj and Umrah Crowd Management Dataset (2024)

- **Source:** Kaggle  
- **Dataset Name:** Hajj and Umrah Crowd Management Dataset 2024  
- **URL:** https://www.kaggle.com/datasets/yarayahyahassan/hajj-and-umrah-crowd-management-dataset-2024  
- **Date of Collection:** 28 Feb 2026  
- **File Format:** CSV  
- **Time Coverage:** 2024 (Hajj season)  
- **Geographic Scope:** Hajj-related locations (e.g., Makkah, Mina, Arafat, etc., if specified)  
- **Collection Method:** Direct manual download from Kaggle platform  
- **Storage Location:** `/Raw_Data/hajj_raw.csv`

### Initial Observations:

- Dataset focuses on crowd management during Hajj 2024.
- Likely contains:
  - Date
  - Time (if available)
  - Location
  - Crowd density / number of pilgrims
- Data granularity may be:
  - Daily
  - Hourly
  - Event-based
- Limited to a single year (2024).
- May require alignment with weather data based on date and time.

---

# 4. Data Storage Structure

The following project structure was created:

```
/Project_Name
   /Raw_Data
      weather_raw.csv
      hajj_raw.csv
   Logbook_Phase2_Data_Collection.md
```

All datasets were saved in their original format without any modification.

---

# 5. Planned Integration Strategy

The datasets will be merged based on shared keys.

## Primary Merge Keys:

- `Date`
- `Time` (if available in both datasets)
- Possibly `Location` (if weather dataset contains city-level data)

## Merge Objective:

To align weather conditions with Hajj crowd levels for the same time period to analyze potential relationships between:

- Temperature and crowd density
- Humidity and crowd flow
- Wind speed and crowd management patterns

---

# 6. Identified Challenges (Before Cleaning)

1. Time Coverage Mismatch:
   - Weather dataset: 2020–2025
   - Hajj dataset: 2024 only

   → Only 2024 weather data will be used during integration.

2. Granularity Difference:
   - Weather data: Daily
   - Hajj data: Possibly hourly or event-based

   → May require time aggregation or transformation.

3. Date Format Differences:
   - Possible different formats (e.g., YYYY-MM-DD vs DD/MM/YYYY)

4. Missing Values:
   - Weather dataset may contain null entries.

5. Location Matching:
   - Weather dataset may include multiple cities.
   - Hajj dataset may include specific Hajj sites.
   - Filtering for Makkah region may be required.

---

# 7. Summary

- Both datasets were successfully collected.
- No modifications were made to the raw files.
- Data saved in Raw_Data folder.
- Initial review suggests integration is feasible using Date and Time keys.
- Further processing and cleaning will be handled in the next phase.

---

**End of Data Collection Logbook – Phase 2**
