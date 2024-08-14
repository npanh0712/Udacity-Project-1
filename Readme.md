# Seattle Airbnb Data Analysis
This project analyzes Airbnb listing data for Seattle to answer several key business questions. The analysis is conducted using Python and follows the CRISP-DM methodology. 
**The project will address the following questions:**
*1. Which month is the busiest time for Airbnb in Seattle? Which day of the week is the busiest?*
*2. How much does the price change month by month in a year?*
*3. What is the average review score for Airbnb in Seattle? Is the distribution of review scores skewed?*
*4. How is the correlation of number of reviews and review score?*
*5. Which day of week is booked the most?*
*6. What does the average listing price trend by month look like?*
**Metrics for answering those questions are:**
*1. The number of reviews vs. review score per listing*
*2. The price of listings changed by time*
*3. The number of bookings changed by time*

# Project Breakdown
Using Seattle AirBnb data on Kaggle
*1. Listings.csv: describing the housing and including the review score*
*2. Reviews.csv: containing the housing ID and reviews in detail*
*3. Calendar.csv: the price, housing ID and availability for the day*

# Prerequisites
*Python 3.x*
*Libraries:* pandas, numpy, matplotlib, seaborn

# Code Breakdown
## Loading The Data
```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from scipy.stats import skew
# Import data
house_listings_df = pd.read_csv("/Users/yengmacbook/Downloads/archive/listings.csv")
house_calendar_df = pd.read_csv("/Users/yengmacbook/Downloads/archive/calendar.csv")
house_reviews_df = pd.read_csv("/Users/yengmacbook/Downloads/archive/reviews.csv")
# Show 5 first rows of each dataset
print(house_listings_df.head())
print(house_calendar_df.head())
print(house_reviews_df.head())
```

## Data Preparation
```python
# Check data types and missing values
print(house_listings_df.info())
print(house_calendar_df.info())
print(house_reviews_df.info())
# Check for missing data
print(house_listings_df.isnull().sum())
print(house_calendar_df.isnull().sum())
print(house_reviews_df.isnull().sum())
# Impute missing values with mean for numeric columns
house_listings_df['review_scores_rating'] = house_listings_df['review_scores_rating'].fillna(house_listings_df['review_scores_rating'].mean())
# Impute the most frequent value for categorical columns
house_listings_df['host_is_superhost'] =  house_listings_df['host_is_superhost'].fillna(house_listings_df['host_is_superhost'].mode()[0])
# Data cleaning for Price column
house_calendar_df['price'] = house_calendar_df['price'].replace(r'\$|,', '', regex=True)
# Replace NaN value with 0
house_calendar_df['price'] = house_calendar_df['price'].fillna(0)
```

## Data Analysis
The rest of the code

