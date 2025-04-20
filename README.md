# E-Commerce Sales, Cohort & RFM Analysis Dashboard (Power BI)
### Project Overview
This project presents an interactive Power BI dashboard for analyzing e-commerce sales data. It includes sales performance, customer behavior analysis through cohort and RFM segmentation. Insights are visualized to help businesses improve retention and identify high-value customers.

### Data Source
E-commerce sales dataset (Excel format)

### Tools Used
  1. Microsoft Excel
  2. Power BI

### Data Cleaning (in Excel & Power BI)
  1. Removed irrelevant columns for optimization
  2. Handled missing values in key fields
  3. Corrected data types for dates and numeric fields
  4. Standardized categorical values (e.g., region, ship mode)
  5. Created new calculated columns (e.g., Year, Month, Customer Age)

### Exploratory Data Analysis (EDA)
  1. Univariate analysis for sales, profit, orders
  2. Bivariate analysis for region vs. sales/profit
  3. Time series breakdown for YoY comparison
  4. Product-category level performance
  5. State-wise contribution to profit and sales

### Data Analysis (Power BI Visuals)
#### Sales Dashboard (Page 1)
  1. Sales, Profit, and Return comparison (Current vs Previous Year) using KPI cards
  2. Slicers for dynamic filtering (State, Region, Category, Ship Mode)
  3. Top-performing products by profit (bar chart)
  4. Sales trend vs previous year (line chart)
  5. Geo map for visualizing Profit, Orders, and Customer Count by location

#### Cohort & RFM Analysis (Page 2)
  1. Cohort Analysis Matrix: Quarterly new customer counts and number of customers retention
  2. Retention Matrix: Percentage of customers retained quarter over quarter
  3. RFM Analysis: Treemap showing 10 customer segments based on RFM scores

### Results / Findings
  1. Most sales and profits came from the Western and Central regions
  2. Office Supplies had high sales but relatively low profit margins
  3. High-value customers fall under "Champions" and "Loyal Customers" in RFM
  4. Customer retention drops sharply after the first 3 months
  5. Certain states have high return rates, affecting overall profit

### Recommendations
  1. Focus retention efforts within the first 90 days of customer onboarding
  2. Launch loyalty programs targeted at "Loyal Customers" and "Potential Loyalists"
  3. Improve return policy or product quality in high-return states
  4. Optimize inventory for high-profit products and ship modes
  5. Tailor marketing campaigns by region and customer segment

### DAX for RFM and Cohort Analysis

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
