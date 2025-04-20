# E-Commerce Sales, Cohort & RFM Analysis Dashboard (Power BI)
### Table of Contents:
- [Project Overview](#project-overview)
- [Data Source](#data-source)
- [Tools Used](#tools-used)
- [Data Cleaning (in Excel & Power BI)](#data-cleaning-in-excel--power-bi)
  - [Exploratory Data Analysis](#exploratory-data-analysis)

### Project Overview:
This project presents an interactive Power BI dashboard for analyzing e-commerce sales data. It includes sales performance, customer behavior analysis through cohort and RFM segmentation. Insights are visualized to help businesses improve retention and identify high-value customers.

### Data Source:
E-commerce sales dataset: [Download here](https://github.com/user-attachments/files/19825605/Superstore.xls)

### Tools Used:
  1. Microsoft Excel
  2. Power BI

### Data Cleaning (in Excel & Power BI):
  1. Removed irrelevant columns for optimization
  2. Handled missing values in key fields
  3. Corrected data types for dates and numeric fields
  4. Standardized categorical values (e.g., region, ship mode)
  5. Created new calculated columns (e.g., Year, Month, Customer Age)

### Exploratory Data Analysis (EDA):
  1. Univariate analysis for sales, profit, orders
  2. Bivariate analysis for region vs. sales/profit
  3. Time series breakdown for YoY comparison
  4. Product-category level performance
  5. State-wise contribution to profit and sales

### DAX for RFM and Cohort Analysis:

  1. Customer Segmentation – RFM Logic (DAX)

    Customer Segment = 
    SWITCH(TRUE(),
        [RFM score] <= 211, "Lapsed",
        [RFM score] <= 232, "At Risk",
        [RFM score] <= 244, "Hibernating",
        [RFM score] <= 321, "About to Sleep",
        [RFM score] <= 333, "Need Attention",
        [RFM score] <= 354, "Potential Loyal Customers",
        [RFM score] <= 412, "New Customers",
        [RFM score] <= 444, "Loyal Customers",
        [RFM score] <= 533, "Potential VIP",
        "VIP Customers"
    )

  2. Cohort DAX for each Quarter (Row)

    Cohort Qtr = 
    VAR CurrentCustomer = Orders[Customer ID]
    VAR OrderDate = CALCULATE(EOMONTH(MIN(Orders[Order Date]), 0), FILTER(Orders, Orders[Customer ID] = CurrentCustomer))
    VAR EndofQtr = 
        SWITCH(
            CEILING(MONTH(OrderDate) / 3, 1),
            1, DATE(YEAR(OrderDate), 1, 1),  -- Q1: Jan 1
            2, DATE(YEAR(OrderDate), 4, 1),  -- Q2: Apr 1
            3, DATE(YEAR(OrderDate), 7, 1),  -- Q3: Jul 1
            4, DATE(YEAR(OrderDate), 10, 1)  -- Q4: Oct 1
        )
    RETURN EndofQtr

  3. Cohort DAX for each Quarter (Column)

    QuarterAfterFirstOrder = 
    VAR FirstOrderDate = RELATED('Cohort'[First Order])
    VAR EndOfQuarterDate = RELATED('Date'[End of Qtr])
    VAR QuartersDifference = DATEDIFF(FirstOrderDate, EndOfQuarterDate, QUARTER)
    RETURN QuartersDifference + 1

  4. Customer Retention Percentage for each Quarter 

    % Cohorts = 
    VAR CurrentQuarter = SELECTEDVALUE(Orders[QuarterAfterFirstOrder])
    
    VAR FirstQuarterCustomers = 
        CALCULATE(
            DISTINCTCOUNT(Orders[Customer ID]), 
            Orders[QuarterAfterFirstOrder] = 1
        )
    
    VAR CurrentQuarterCustomers = 
        CALCULATE(
            DISTINCTCOUNT(Orders[Customer ID]), 
            Orders[QuarterAfterFirstOrder] = CurrentQuarter
        )
    
    RETURN 
    IF(
        CurrentQuarter = 1,
        1,  // For Quarter 1, we return 100%
        DIVIDE(
            CurrentQuarterCustomers,  // Numerator: Customers in the current quarter
            FirstQuarterCustomers  // Denominator: Customers in the first quarter
        )
    )

### Power BI Optimization:
To improve the performance and efficiency of my Power BI report, I applied the following best practices:
  1. **Used a star schema:** Structured the data model with fact and dimension tables instead of flat tables to reduce redundancy and optimize query performance.
  2. **Removed unnecessary columns and rows:** Imported only the required columns and filtered out irrelevant rows to minimize memory usage and improve refresh speed.
  3. **Turned off Auto Date/Time:** Disabled the Auto Date/Time feature to prevent Power BI from creating unnecessary hidden date tables that impact performance.
  4. **Optimized DAX by using measures:** Replaced calculated columns with DAX measures wherever possible to improve calculation performance and reduce memory load.

Snap of Schema

![Image](https://github.com/user-attachments/assets/7d13f3d8-1df6-4e79-9595-c2491885084f)

### Data Analysis (Power BI Visuals):
#### Sales Dashboard (Page 1):
  1. Sales, Profit, and Return comparison (Current vs Previous Year) using KPI cards
  2. Slicers for dynamic filtering (State, Region, Category, Ship Mode)
  3. Top-performing products by profit (bar chart)
  4. Sales trend vs previous year (line chart)
  5. Geo map for visualizing Profit, Orders, and Customer Count by location

Snap of Sales Dashboard

![Image](https://github.com/user-attachments/assets/b8296dbd-f119-4f54-8ed6-fdb1cb2a57e6)

#### RFM Analysis & Cohort (Page 2):
  1. Cohort Analysis Matrix: Quarterly new customer counts and number of customers retention
  2. Retention Matrix: Percentage of customers retained quarter over quarter
  3. RFM Analysis: Treemap showing 10 customer segments based on RFM scores

Snap of RFM & Cohort Analysis

![Image](https://github.com/user-attachments/assets/68cd8002-9481-4827-a859-09beb8a190ae)

### Results / Findings:
  1. Most sales and profits came from the Western and Eastern regions
  2. Sub-categories like Tables, Bookcases, and Supplies consistently generate losses.
  3. Corporate and Home Office segments show low Sales/Profit ratio due to a high volume of return orders.
  4. While the overall profit from 49 states is $286,000, five states — North Carolina, Illinois, Pennsylvania, Ohio, and Texas — contribute to a loss of $78,400 which is around 30% Loss.
  5. Approximately 30% of customers fall under Lapsed, Hibernating, and About to Sleep segments in RFM analysis.
  6. Only around 20% of customers are retained quarter-over-quarter, indicating low long-term engagement.

### Recommendations:
  1. **Launch loyalty programs** targeted at "Loyal Customers" and "Potential Loyalists" to nurture them into "VIP Customers".
  2. **Leverage RFM segments to design personalized offers** for VIP and Loyal Customers to encourage repeat purchases.
  3. **Introduce customer feedback loops** for "At Risk", "About to Sleep", and "Hibernating" segments to understand reasons for disengagement.
  4. **Run limited-time promotions** targeting “Potential Loyal Customers” and "Need Attention" segments to push them into higher-value groups.
  5. **Discontinue or reevaluate underperforming sub-categories** like Tables and Bookcases; consider bundling, discounting, or replacing with better-performing items.
  6. **Investigate reasons for high return rates** in Corporate and Home Office segments; improve product information, quality checks, and return handling.
  7. **Develop targeted recovery strategies for loss-making states** through localized campaigns, offers, and customer engagement efforts.
  8. **Re-engage dormant customers** (Lapsed, Hibernating, About to Sleep) via personalized reactivation emails, surveys, or special win-back incentives.

