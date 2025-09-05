# Real Estate Analysis and Preprocessing - Mexico City Housing Data

**Capstone Project**: Analysis and preprocessing of Mexico City real estate data for machine learning applications and dashboard insights.

## Project Overview

This project performs comprehensive data cleaning, feature engineering, and preprocessing on a Mexico City housing dataset (`housing_data_CDMX_v2.csv`) containing 18,234 properties. The goal is to prepare clean, ML-ready data and extract insights through visualization and an interactive dashboard.

**Interactive Dashboard**: [Real Estate Dashboard - Mexico City](https://public.tableau.com/app/profile/mohamed.younes4248/viz/SIC_P1/Dashboard?publish=yes)

## Dataset Information

- **Original Size**: 18,234 records with 13 features
- **Final Size**: ~14,355 records after cleaning
- **Key Features**: Property type, location (places), coordinates, price (USD), surface areas, price per m²

### Original Columns
- `property_type`: apartment, house, store, PH
- `places`: Mexico City neighborhoods/areas  
- `lat-lon`: Combined coordinates
- `price`, `currency`, `price_aprox_local_currency`, `price_aprox_usd`: Price in different formats
- `surface_total_in_m2`, `surface_covered_in_m2`: Property areas
- `price_usd_per_m2`, `price_per_m2`: Price per square meter
- `lat`, `lon`: Separate coordinates

## Data Preprocessing Pipeline

### 1. Initial Data Cleaning
- **Duplicate Removal**: Dropped duplicate records
- **Currency Standardization**: Standardized all prices to USD only, removed MXN and redundant price columns
- **Feature Engineering**: Created `surface_uncovered_in_m2 = surface_total_in_m2 - surface_covered_in_m2`

### 2. Data Quality Issues Addressed

#### Logical Errors
- **Negative Uncovered Space**: Removed 880 rows where `surface_uncovered_in_m2 < 0` (logical impossibility)
- **Extreme Outliers**: Dropped properties with `surface_uncovered_in_m2 > 20,000 m²` (data entry errors)
- **Abnormal Pricing**: Removed properties with `price_usd_per_m2 < 1` (unrealistic prices)

#### Property-Specific Filters
- **Apartments**: Removed those with `surface_covered_in_m2 > 1,500 m²`
- **Houses**: Removed those with `surface_covered_in_m2 > 2,000 m²` 
- **All Properties**: Removed those with `surface_covered_in_m2 > 5,000 m²`

### 3. Geographic Filtering
- **Coordinate Processing**: Split `lat-lon` column using regex to handle both comma and colon separators
- **Mexico City Boundaries**: Restricted data to Mexico City coordinates:
  - Latitude: 19.0 to 19.6
  - Longitude: -99.33 to -98.97

### 4. Feature Engineering & Encoding
- **One-Hot Encoding**: Applied to `places` (neighborhoods) and `property_type`
- **Boolean Conversion**: Converted True/False values to 1/0

### 5. Feature Scaling & Normalization

#### Price Variables
- **Log Transformation**: Applied to `price_aprox_usd` → `normalized_price`
- **StandardScaler**: Applied to normalized price → `scaled_price`

#### Price per m²
- **Log Transformation**: Applied to `price_usd_per_m2` → `normalized_price_m2`  
- **StandardScaler**: Applied to normalized price per m² → `scaled_price_m2`

#### Surface Areas
- **Total Area**: Log transformation → `normalized_totalArea` (no standardization due to persistent skewness)
- **Covered Area**: Log transformation → `normalized_surface_covered_in_m2`
- **Uncovered Area**: 
  - Log1p transformation → `normalized_surface_uncovered_in_m2`
  - RobustScaler → `surface_uncovered_scaled`
  - MaxAbsScaler with log1p → `scaled_surface_uncovered`

### 6. Final Cleanup
- Removed original coordinate columns (`lat`, `lon`)
- Dropped intermediate normalized columns, keeping only scaled versions
- Exported clean dataset to `housing.xlsx`

## Key Insights from Analysis

1. **Currency Impact**: Strong correlation between price fields varied by currency (MXN vs USD)
2. **Property Types**: Different property types show distinct size and price patterns
3. **Geographic Distribution**: Properties concentrated in specific Mexico City areas
4. **Data Quality**: Significant cleaning required due to data entry errors and outliers
5. **Feature Relationships**: Surface areas show expected positive correlation with prices

## Files in Repository

- `Main_notebook.ipynb`: Complete analysis and preprocessing notebook
- `housing_data_CDMX_v2.csv`: Original raw dataset
- `housing.xlsx`: Cleaned and processed dataset (generated)
- `_Real Estate Price Analysis and Preparation For ML_final.pptx`: Project presentation
- `README.md`: This documentation

## Dependencies

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.preprocessing import StandardScaler, RobustScaler, MaxAbsScaler
```

## Usage

1. Clone the repository
2. Install required packages: `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`
3. Run `Main_notebook.ipynb` cell by cell to reproduce the analysis
4. The final processed dataset will be saved as `housing.xlsx`

## Next Steps

The processed dataset is now ready for:
- Machine learning model training (price prediction, classification)
- Advanced geospatial analysis
- Time series analysis (if temporal data is added)
- Integration with additional Mexico City datasets

## Dashboard

Explore the interactive Tableau dashboard for visual insights: [Real Estate Dashboard - Mexico City](https://public.tableau.com/app/profile/mohamed.younes4248/viz/SIC_P1/Dashboard?publish=yes)
