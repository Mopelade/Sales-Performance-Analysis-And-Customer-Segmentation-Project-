# Sales-Performance-Analysis-And-Customer-Segmentation-Project-

## Table Of Content 
   Introduction
   
   Objective 
   
   SoftWare Listing 
   
   ## Data Analysis 

   SQL Code Breakdown 

   PowerBI Dashboard 

   Insights 

   Recommedation 

# Introduction 
   
Understanding customer behavior and transactional patterns in the evolving era of online retail is essential for driving strategic decision-making and optimizing sales strategies. This analysis aims to explore UK-based online retail transactions from December 1, 2009, to December 9, 2011. The dataset includes all-occasion giftware sales, providing insights into consumer preferences, purchasing habits, and market trends within the specified timeframe.

# Objective 

The objective is to examine various aspects of sales performance, customer behavior, and product performance, as well as conduct a temporal analysis. 

### Sales Performance

**Total Revenue Analysis:**
- What is the total revenue, quantity, order count, and average price over time (monthly, quarterly, annually)?
- How do sales metrics trend over different periods (seasonality)?

**Average Order Value (AOV):**
- What is the overall AOV across all customers and regions?
- How does AOV vary by region/country?

**Revenue by Region:**
- Which regions or countries generate the highest and lowest revenue?
- How does the revenue distribution look across different regions?

**Revenue by Product:**
- Which products contribute the most to total revenue?
- What is the revenue distribution across different product categories?

### Customer Behavior

**Customer Segmentation:**
- How can customers be segmented based on their purchase behavior (e.g., high spenders, frequent buyers)?
- What is the revenue contribution from different customer segments?

### Product Performance

**Product Popularity:**
- Which products are the best-sellers?
- What are the top-performing product categories?

### Temporal Analysis

**Busiest Time Periods:**
- What are the busiest times of the day, week, or month for sales?

# Software Listing
The analysis utilized specific software tools to segment customers and visualize the data effectively. 

- **SQL Server** was used to segment customers based on Recency, Frequency, and Monetary value (RFM).
- **Power BI** was used for data visualization.

This combination of SQL Server and Power BI enabled detailed customer segmentation and provided clear, interactive visual representations of the data for strategic decision-making.

# Data Analysis 
To perform the RFM Analysis on SQL. 
- **Step 1** :Create a Table to Store RFM Scores and Segment Information 

```sql
 CREATE TABLE RFM(
    CustomerID Int,
    Recency INT,
    Frequecy INT,
    Monetary FLOAT,
    RFM_segment VARCHAR(10)
    );
```

- **Step 2**: Calculate the Recency, Frequency and Monetar for each customer.

```sql
with RFM_analysis as (
        SELECT 
        CustomerID, 
        DATEDIFF(day,MAX(INVOICEDATE),'2011-12-09' ) AS RECENCY,
        COUNT(DISTINCT InvoiceNo) as FREQUENCY,
        SUM(Revenue) AS MONETARY
    FROM RFMOnlineRetail
    GROUP BY CustomerID
    )
```
- **Step 3**: Insert data into RFM table and  Assign RFM Scores
```sql 
INSERT INTO RFM (CustomerID,Recency,Frequecy,Monetary)
     SELECT 
     CustomerID, 
     NTILE(5) OVER (ORDER BY RECENCY DESC),
     NTILE(5) OVER (ORDER BY FREQUENCY ),
     NTILE(5) OVER (ORDER BY MONETARY  )
     FROM RFM_analysis;
```
**NTILE FUNCTION** was used to rank customers based on their recency ,Frequency, Monetary . The Function divided the customers into 5 groups based on their recency ,Frequency, Monetary
For Recency, customers with the lowest recency get a higher score(5) and customers with the highest recency 
get a lower score(1). Frequency and Monetary, customer with the highest numbers get higher score(5) and customers with lower numbers, get low lower score (1).  The resulting scores (from 1 to 5) are inserted into the RFM table. 

- **Step 4**: Add a new column named customer segement   
```sql 
ALTER TABLE RFM
    add Customer_segement nvarchar(50);
```

**Concatenet The RFM Score**
```sql
 UPDATE RFM
    SET RFM_segment = CONCAT(RECENCY,Frequecy,MONETARY)
```

- **Step 5**: Segment customers based on the RFM SCORES
```sql
select CustomerID,Recency, Frequecy,Monetary,RFM_segment,
        case when [Recency] >= 4 and [Frequecy] >= 4 and [Monetary] >= 4 then 'Champion'
             when [Recency] >= 2 and [Frequecy] <= 2 and [Monetary] >= 4 then  'Big Spenders'
             when [Recency] >= 3 and [Frequecy] >= 3 and [Monetary] >= 3 then 'Loyal Customer'
             when [Recency] >= 4 and [Frequecy] <= 2 and [Monetary] <= 3 then 'New Customers'
             when [Recency] >= 3 and [Frequecy] >= 3 and [Monetary] <= 2 then  'Low Spender'
             when [Recency] >= 2 and [Frequecy] >= 2 and [Monetary] >= 2 then 'Promising'
             when [Recency] <= 1 and [Frequecy] >= 3 and [Monetary] >= 3 then  'Can not lose'
             when [Recency] >= 2 and [Frequecy] >= 1 and [Monetary] >= 1 then  'About To sleep'
             when [Recency] <= 1 and [Frequecy] >= 2 and [Monetary] >= 1 then   'At Risk'
                             
 else 'lost'
            end as Customer_segment
From RFM
 ```

#Power BI Dashboard 

![](RFMANALYIS/Salesperformance.png)

### Dashboard Overview

The dashboard shows total sales using different sales metrics. It provides insights into the previous month when you filter for a specific month and shows the percentage change.

In another part of the dashboard, you can see the top and bottom performing countries and SKUs, dynamically displaying rankings from the top 10 to the top 1. It also allows you to view performance using multiple sales metrics in a single visualization.

![](RFMANALYIS/customersegementation.png)

### Customer Segmentation Dashboard Overview

The dashboard segments customers using RFM (Recency, Frequency, Monetary) analysis. Customer RFM scores are divided into five ranks (1 to 5):

- **Recency:**
  - Customers with recent purchases are assigned scores of **1 or 2**.
  - Customers with moderately recent purchases are assigned a score of **3**.
  - Customers with older purchases are assigned scores of **4 or 5**.

- **Frequency and Monetary:**
  - Customers with low purchase frequency and spending are assigned scores of **1 or 2**.
  - Customers with moderate purchase frequency and spending are assigned a score of **3**.
  - Customers with high purchase frequency and spending are assigned scores of **4 or 5**.

For each segment, the dashboard displays the total number of customers and total sales. Additionally, recommended marketing actions are provided for each segment.

![](RFMANALYIS/timebased.png)

### Time-Based Sales Performance Dashboard

The dashboard provides an overview of sales performance over time, allowing users to see sales trends across multiple periods (year, month, weekday, week of the year) using various sales metrics, including:

- **Revenue**
- **Quantity**
- **Order Count**
- **Average Order Value (AOV)**
- **Average Unit Price**

Key features of the dashboard include:

- **Month-over-Month (MOM) Growth:**
  - Displays MOM growth by revenue to track sales performance changes month by month.

- **Busiest Days and Hours:**
  - Highlights the busiest days and hours with the highest number of orders, helping users understand peak sales times.
