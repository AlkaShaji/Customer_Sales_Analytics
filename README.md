# Customer Sales Behavior Analysis
Python • SQL Server • Power BI • End-to-End Data Analytics Project

## Project Overview 

This project analyzes customer shopping behavior using Python, SQL Server, and Power BI.
The goal is to uncover insights related to:

Customer demographics
Purchase patterns
Product performance
Subscription behavior
Discount impact
Revenue contribution

The workflow includes data cleaning → SQL analysis → dashboard visualization.

## Tech Stack

Python → Data cleaning, preprocessing, transformation
SQL Server → Data storage, advanced analytical queries
Power BI → Dashboard visualization
Libraries: Pandas, NumPy, SQLAlchemy, pyodbc

## Python Tasks (Data Cleaning)

The data preparation process includes:

✔ Handling missing values
✔ Renaming columns into snake_case
✔ Creating age_group using pd.qcut
✔ Creating purchase_frequency_days using mapping
✔ Dropping duplicate or unnecessary columns
✔ Uploading cleaned data to SQL Server using SQLAlchemy

## 📁 Python notebook:
python/data_cleaning_and_preprocessing.ipynb

## 🗄 SQL Server Analysis
Key Questions Answered

✔ Total revenue by gender
✔ Customers using discount but spending above average
✔ Top 5 products by highest average review rating
✔ Average purchase amount by shipping type
✔ Do subscribed customers spend more?
✔ Top 5 products with highest discount usage %
✔ Customer segmentation (New / Returning / Loyal)
✔ Top 3 most purchased products within each category
✔ Are repeat buyers likely to subscribe?
✔ Revenue contribution by each age group

## SQL Queries:
sql/business_queries.sql

## Power BI Dashboard Features
Dashboard Includes:

#### KPIs:
‣ Total Revenue
‣ Avg Purchase Amount
‣ Avg Review Rating
‣ Total Customers

#### Visuals:
✔ Revenue by Category
✔ Sales by Category
✔ % Subscribers
✔ Age Group Performance
✔ Payment Method Distribution
✔ Revenue by Shipping Type
✔ Top Products & Category Insights

#### Interactive Slicers:
✔ Gender
✔ Category
✔ Subscription Status
✔ Shipping Type

#### 📁 Dashboard File:
powerbi/customer_behavior_dashboard.pbix

#### 📁 Dashboard Screenshots:

<img width="1351" height="759" alt="image" src="https://github.com/user-attachments/assets/92f7a4dc-8c17-41e9-909e-77e07d72f279" />

