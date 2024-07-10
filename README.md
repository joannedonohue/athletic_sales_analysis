# athletic_sales_analysis

# Athletic Store Sales Analysis

This project analyzes athletic store sales data from across the US for the years 2020 and 2021. The analysis includes determining which region sold the most products, which retailer had the most sales, and which day and week had the most women's athletic footwear sales.

## Table of Contents

1. [Installation](#installation)
2. [Usage](#usage)
3. [Project Structure](#project-structure)
4. [Data Cleaning and Preparation](#data-cleaning-and-preparation)
5. [Analysis Steps](#analysis-steps)
    - [Combine and Clean the Data](#1-combine-and-clean-the-data)
    - [Determine which Region Sold the Most Products](#2-determine-which-region-sold-the-most-products)
    - [Determine which Region had the Most Sales](#3-determine-which-region-had-the-most-sales)
    - [Determine which Retailer had the Most Sales](#4-determine-which-retailer-had-the-most-sales)
    - [Determine which Retailer Sold the Most Women's Athletic Footwear](#5-determine-which-retailer-sold-the-most-womens-athletic-footwear)
    - [Determine the Day with the Most Women's Athletic Footwear Sales](#6-determine-the-day-with-the-most-womens-athletic-footwear-sales)
    - [Determine the Week with the Most Women's Athletic Footwear Sales](#7-determine-the-week-with-the-most-womens-athletic-footwear-sales)

## Installation

1. Clone the repository to your local machine.
   ```sh
   git clone <repository-url>
   ```

2. Navigate to the project directory.
   ```sh
   cd athletic-store-sales-analysis
   ```

3. Install the required dependencies.
   ```sh
   pip install pandas
   ```

## Usage

1. Ensure you have the required CSV files (`athletic_sales_2020.csv` and `athletic_sales_2021.csv`) in the `Resources` directory.
2. Run the Jupyter Notebook or Python script to perform the analysis.

## Project Structure

```plaintext
athletic-store-sales-analysis/
│
├── Resources/
│   ├── athletic_sales_2020.csv
│   ├── athletic_sales_2021.csv
│
├── analysis.ipynb
├── README.md
```

## Data Cleaning and Preparation

### 1. Combine and Clean the Data

#### Import Libraries and Dependencies

```python
import pandas as pd
from pathlib import Path
```

#### Import CSVs

Read the CSV files into DataFrames.

```python
path1 = Path('Resources/athletic_sales_2020.csv')
path2 = Path('Resources/athletic_sales_2021.csv') 

ath_2020 = pd.read_csv(path1)
ath_2021 = pd.read_csv(path2)
```

Check the data types and combine the sales data by rows.

```python
ath_sales = pd.concat([ath_2020, ath_2021]).reset_index(drop=True)
ath_sales["invoice_date"] = pd.to_datetime(ath_sales["invoice_date"], format='%m/%d/%y')
```

## Analysis Steps

### 2. Determine which Region Sold the Most Products

Using `groupby`:

```python
ath_units_grouped = ath_sales.groupby(["region", "state", "city"]).agg({"units_sold": "sum"}).rename(columns={"units_sold": "Total_Products_Sold"})
ath_units_sorted = ath_units_grouped.sort_values(by=["Total_Products_Sold"], ascending=False)
ath_units_sorted.head(5)
```

Using `pivot_table`:

```python
ath_units_pivot = ath_sales.pivot_table(values="units_sold", index=["region", "state", "city"], aggfunc="sum")
ath_units_pivot.columns=["Total_Products_Sold"]
ath_units_pivot_sorted = ath_units_pivot.sort_values(by="Total_Products_Sold", ascending=False)
ath_units_pivot_sorted.head(5)
```

### 3. Determine which Region had the Most Sales

Using `groupby`:

```python
ath_sales_grouped = ath_sales.groupby(["region", "state", "city"]).agg({"total_sales": "sum"}).rename(columns={"total_sales": "Total_Sales"})
ath_sales_sorted = ath_sales_grouped.sort_values(by=["Total_Sales"], ascending=False)
ath_sales_sorted.head(5)
```

Using `pivot_table`:

```python
ath_sales_pivot = ath_sales.pivot_table(values="total_sales", index=["region", "state", "city"], aggfunc="sum")
ath_sales_pivot.columns=["Total_Sales"]
ath_sales_pivot_sorted = ath_sales_pivot.sort_values(by="Total_Sales", ascending=False)
ath_sales_pivot_sorted.head(5)
```

### 4. Determine which Retailer had the Most Sales

Using `groupby`:

```python
retail_sales_grouped = ath_sales.groupby(["retailer","region", "state", "city"]).agg({"total_sales": "sum"}).rename(columns={"total_sales": "Total_Sales"})
retail_sales_sorted = retail_sales_grouped.sort_values(by=["Total_Sales"], ascending=False)
retail_sales_sorted.head(5)
```

Using `pivot_table`:

```python
retail_sales_pivot = ath_sales.pivot_table(values="total_sales", index=["retailer","region", "state", "city"], aggfunc="sum")
retail_sales_pivot.columns=["Total_Sales"]
retail_sales_pivot_sorted = retail_sales_pivot.sort_values(by="Total_Sales", ascending=False)
retail_sales_pivot_sorted.head(5)
```

### 5. Determine which Retailer Sold the Most Women's Athletic Footwear

Filter the sales data to get the women's athletic footwear sales data.

```python
womens = ath_sales.loc[(ath_sales["product"] == "Women's Athletic Footwear")]
```

Using `groupby`:

```python
womens_grouped = womens.groupby(["retailer","region", "state", "city"]).agg({"units_sold": "sum"}).rename(columns={"units_sold": "Womens_Footwear_Units_Sold"})
womens_sorted = womens_grouped.sort_values(by=["Womens_Footwear_Units_Sold"], ascending=False)
womens_sorted.head(5)
```

Using `pivot_table`:

```python
womens_pivot = womens.pivot_table(values="units_sold", index=["retailer","region", "state", "city"], aggfunc="sum")
womens_pivot.columns=["Womens_Footwear_Units_Sold"]
womens_pivot_sorted = womens_pivot.sort_values(by="Womens_Footwear_Units_Sold", ascending=False)
womens_pivot_sorted.head(5)
```

### 6. Determine the Day with the Most Women's Athletic Footwear Sales

Create a pivot table with the 'invoice_date' column as the index, and the "total_sales" as the values.

```python
womens_daily_pivot = womens.pivot_table(values="total_sales", index=["invoice_date"], aggfunc="sum")
womens_daily_pivot.columns=["Total_Sales"]
```

Resample the pivot table into daily bins and sort.

```python
womens_daily_resampled = womens_daily_pivot.resample("D").sum()
womens_daily_sorted = womens_daily_resampled.sort_values(by="Total_Sales", ascending=False)
womens_daily_sorted.head(10)
```

### 7. Determine the Week with the Most Women's Athletic Footwear Sales

Resample the pivot table into weekly bins and sort.

```python
womens_weekly_resampled = womens_daily_pivot.resample("W").sum()
womens_weekly_sorted = womens_weekly_resampled.sort_values(by="Total_Sales", ascending=False)
womens_weekly_sorted.head(10)
```
```
