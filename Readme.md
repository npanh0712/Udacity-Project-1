# Seattle Airbnb Data Analysis
## Business Understanding
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

## Data Understanding
Using Seattle AirBnb data on Kaggle
*1. Listings.csv: describing the housing and including the review score*
*2. Reviews.csv: containing the housing ID and reviews in detail*
*3. Calendar.csv: the price, housing ID and availability for the day*

## Prerequisites
*Python 3.x*
*Libraries:* pandas, numpy, matplotlib, seaborn

## Prepare Data
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

## Data Wrangle & Clean
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

## Question 1
*Which month is the busiest time for Airbnb in Seattle? Which day of the week is the busiest?*
```python
# Convert date columns to datetime
house_calendar_df['date'] = pd.to_datetime(house_calendar_df['date'])
house_reviews_df['date'] = pd.to_datetime(house_reviews_df['date'])

# Extract month and day of the week
house_calendar_df['month'] = house_calendar_df['date'].dt.month
house_calendar_df['day_of_week'] = house_calendar_df['date'].dt.day_name()

# Group by month and day of week to find the busiest times
busy_month = house_calendar_df.groupby('month')['available'].apply(lambda x: (x == 'f').sum())
busy_day_of_week = house_calendar_df.groupby('day_of_week')['available'].apply(lambda x: (x == 'f').sum())

# Plotting
plt.figure(figsize=(12, 6))

plt.subplot(1, 2, 1)
sns.barplot(x=busy_month.index, y=busy_month.values)
plt.title('Busiest Month')
plt.xlabel('Month')
plt.ylabel('Number of Bookings')

plt.subplot(1, 2, 2)
sns.barplot(x=busy_day_of_week.index, y=busy_day_of_week.values, order=['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'])
plt.title('Busiest Day of the Week')
plt.xlabel('Day of the Week')
plt.ylabel('Number of Bookings')

plt.tight_layout()
plt.show()
```

## Question 2
*What does the average listing price trend by month look like?*
```python
# Group by month and calculate the average price
price_by_month = house_calendar_df.groupby('month')['price'].mean().round(2)

# Ensure the months are in order from January to December
months = range(1, 13)
average_prices = [price_by_month.get(month, 0) for month in months]

# Set up the plot
plt.figure(figsize=(12, 6))

# Line plot for average prices
sns.lineplot(x=months, y=average_prices, marker='o', label='Average Price')

# Formatting the plot
plt.title('Average Airbnb Price by Month in Seattle')
plt.xlabel('Month')
plt.ylabel('Average Price (USD)')
plt.xticks(months)
plt.grid(True)
plt.legend()
plt.tight_layout()
plt.show()
```

## Question 3
*How is the correlation of number of reviews and review score?*
```python
# Scatter plot of review scores vs. number of reviews
plt.figure(figsize=(10, 6))
sns.scatterplot(x=house_listings_df['number_of_reviews'], y=house_listings_df['review_scores_rating'], alpha=0.5)
plt.title('Review Score vs. Number of Reviews')
plt.xlabel('Number of Reviews')
plt.ylabel('Review Score')
plt.show()
```

## Question 4
*What is the average review score for Airbnb in Seattle? Is the distribution of review scores skewed?*
```python
def calculate_average_review_score(house_listings_df):
    """Calculate and return the average review score."""
    return house_listings_df['review_scores_rating'].mean()

def plot_review_score_distribution(house_listings_df):
    """Plot the distribution of review scores."""
    plt.figure(figsize=(10, 6))
    sns.histplot(house_listings_df['review_scores_rating'].dropna(), bins=20, kde=True)
    plt.title('Distribution of Review Scores')
    plt.xlabel('Review Scores')
    plt.ylabel('Frequency')
    plt.grid(True)
    plt.show()

def calculate_skewness(house_listings_df):
    """Calculate and return the skewness of the review score distribution."""
    return skew(house_listings_df['review_scores_rating'].dropna())

def analyze_review_scores(house_listings_df):
    """Analyze the review scores: calculate average, plot distribution, and check skewness."""
    
    # Calculate the average review score
    average_review_score = calculate_average_review_score(house_listings_df)
    print(f"The average review score for Airbnb in Seattle is {average_review_score:.2f}")
    
    # Plot the review score distribution
    plot_review_score_distribution(house_listings_df)
    
    # Calculate the skewness of the review score distribution
    review_score_skewness = calculate_skewness(house_listings_df)
    print(f"The skewness of the review score distribution is {review_score_skewness:.2f}")
    
    # Determine skewness type
    if review_score_skewness > 0:
        print("The distribution of review scores is right-skewed (positively skewed).")
    elif review_score_skewness < 0:
        print("The distribution of review scores is left-skewed (negatively skewed).")
    else:
        print("The distribution of review scores is symmetrical.")
analyze_review_scores(house_listings_df)
```

## Evaluation
Thanks for analyzing the historical data, I’ve learned that there is a much quiet time in Seattle to travel and the listings here are in good condition. I can also prepare my budget as I've already known the price trend.

