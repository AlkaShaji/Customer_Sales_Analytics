"""
🛍️ Customer Shopping Behavior Analytics

End-to-End Data Analytics Project using Python, SQL Server, and Power BI

Steps:
1. Load and clean dataset with pandas
2. Connect to Microsoft SQL Server
3. Upload cleaned data to SQL
4. Execute business insight queries (Q1–Q10)
5. Export results for Power BI visualization
"""


# 📦 1. Import Required Libraries

import pandas as pd
from sqlalchemy import create_engine, text


# 📁 2. Load Dataset

csv_path = "customer_shopping_behavior.csv"
df = pd.read_csv(csv_path)
print("✅ Dataset Loaded Successfully!")
print(f"Rows: {df.shape[0]}, Columns: {df.shape[1]}")
print(df.head(5))


# 🧹 3. Data Cleaning & Feature Engineering


# Fill missing review ratings with median by category
df['Review Rating'] = df.groupby('Category')['Review Rating'].transform(lambda x: x.fillna(x.median()))

# Standardize column names to lowercase and replace spaces with underscores
df.columns = df.columns.str.lower().str.replace(' ', '_')

# Rename key columns for simplicity
df.rename(columns={'purchase_amount_(usd)': 'purchase_amount'}, inplace=True)

# Create Age Group categories using quantiles
age_labels = ['Young Adult', 'Adult', 'Middle-aged', 'Senior']
df['age_group'] = pd.qcut(df['age'], q=4, labels=age_labels)

# Map Frequency of Purchases to numerical days
freq_map = {
    'Weekly': 7, 'Fortnightly': 14, 'Monthly': 30,
    'Quarterly': 90, 'Bi-Weekly': 14, 'Annually': 365, 'Every 3 Months': 90
}
df['purchase_frequency_days'] = df['frequency_of_purchases'].map(freq_map)

print("\n✅ Data Cleaning & Feature Engineering Completed!")
print(df.head(5))


# 🧱 4. Connect Python to Microsoft SQL Server


server = 'sever_name'             # Replace with your SQL Server instance name
database = 'customer_behavior'      # Database name you created in SSMS
driver = 'ODBC Driver 17 for SQL Server'

# Create connection string for Windows Authentication
connection_string = (
    f"mssql+pyodbc://@{server}/{database}"
    f"?driver={driver.replace(' ', '+')}&trusted_connection=yes"
)

# Create SQLAlchemy engine
engine = create_engine(connection_string)

# Test connection
try:
    with engine.connect() as conn:
        print("✅ Successfully connected to SQL Server!")
except Exception as e:
    print("❌ Connection Failed:", e)


# 💾 5. Upload Cleaned Data to SQL Server

table_name = 'customer_sales'

df.to_sql(table_name, con=engine, if_exists='replace', index=False)
print(f"✅ Data uploaded successfully to SQL Server table: {table_name}")


# 🧮 6. SQL Business Questions (Q1–Q10)


queries = {
    # Q1
    "Q1_Total_Revenue_by_Gender": """
        SELECT gender, SUM(purchase_amount) AS total_revenue
        FROM customer_sales
        GROUP BY gender;
    """,

    # Q2
    "Q2_Discount_Above_Average": """
        SELECT customer_id, gender, purchase_amount, discount_applied
        FROM customer_sales
        WHERE discount_applied = 'Yes'
        AND purchase_amount > (SELECT AVG(purchase_amount) FROM customer_sales)
        ORDER BY purchase_amount DESC;
    """,

    # Q3
    "Q3_Top_5_Products_By_Review": """
        SELECT item_purchased AS product_name,
               ROUND(AVG(review_rating), 2) AS avg_rating,
               COUNT(*) AS total_reviews
        FROM customer_sales
        WHERE review_rating IS NOT NULL
        GROUP BY item_purchased
        ORDER BY avg_rating DESC
        OFFSET 0 ROWS FETCH NEXT 5 ROWS ONLY;
    """,

    # Q4
    "Q4_Avg_Purchase_By_Shipping": """
        SELECT shipping_type,
               ROUND(AVG(purchase_amount), 2) AS avg_purchase,
               COUNT(*) AS total_orders
        FROM customer_sales
        GROUP BY shipping_type
        ORDER BY avg_purchase DESC;
    """,

    # Q5
    "Q5_Subscribers_Spend_More": """
        SELECT subscription_status,
               ROUND(AVG(purchase_amount), 2) AS avg_purchase_amount,
               SUM(purchase_amount) AS total_revenue,
               COUNT(*) AS total_orders
        FROM customer_sales
        GROUP BY subscription_status
        ORDER BY avg_purchase_amount DESC;
    """,

    # Q6
    "Q6_Top_5_Discounted_Products": """
        SELECT item_purchased AS product_name,
               COUNT(CASE WHEN discount_applied = 'Yes' THEN 1 END) * 100.0 / COUNT(*) AS discount_percentage,
               COUNT(*) AS total_purchases
        FROM customer_sales
        GROUP BY item_purchased
        ORDER BY discount_percentage DESC
        OFFSET 0 ROWS FETCH NEXT 5 ROWS ONLY;
    """,

    # Q7
    "Q7_Customer_Segmentation": """
        SELECT 
            CASE 
                WHEN previous_purchases = 0 THEN 'New Customer'
                WHEN previous_purchases BETWEEN 1 AND 5 THEN 'Returning Customer'
                WHEN previous_purchases > 5 THEN 'Loyal Customer'
            END AS customer_segment,
            COUNT(*) AS customer_count
        FROM customer_sales
        GROUP BY 
            CASE 
                WHEN previous_purchases = 0 THEN 'New Customer'
                WHEN previous_purchases BETWEEN 1 AND 5 THEN 'Returning Customer'
                WHEN previous_purchases > 5 THEN 'Loyal Customer'
            END
        ORDER BY customer_count DESC;
    """,

    # Q8
    "Q8_Top_3_Products_Per_Category": """
        WITH RankedProducts AS (
            SELECT 
                category,
                item_purchased,
                COUNT(*) AS total_purchases,
                ROW_NUMBER() OVER (PARTITION BY category ORDER BY COUNT(*) DESC) AS rank_within_category
            FROM customer_sales
            GROUP BY category, item_purchased
        )
        SELECT category, item_purchased, total_purchases
        FROM RankedProducts
        WHERE rank_within_category <= 3
        ORDER BY category, total_purchases DESC;
    """,

    # Q9
    "Q9_Repeat_Buyers_Subscription": """
        SELECT 
            CASE WHEN previous_purchases > 5 THEN 'Repeat Buyer' ELSE 'Non-Repeat Buyer' END AS customer_type,
            subscription_status,
            COUNT(*) AS customer_count
        FROM customer_sales
        GROUP BY 
            CASE WHEN previous_purchases > 5 THEN 'Repeat Buyer' ELSE 'Non-Repeat Buyer' END,
            subscription_status
        ORDER BY customer_type, customer_count DESC;
    """,

    # Q10
    "Q10_Revenue_by_Age_Group": """
        SELECT 
            age_group,
            SUM(purchase_amount) AS total_revenue,
            ROUND(
                SUM(purchase_amount) * 100.0 / (SELECT SUM(purchase_amount) FROM customer_sales), 2
            ) AS revenue_percentage
        FROM customer_sales
        GROUP BY age_group
        ORDER BY total_revenue DESC;
    """
}



