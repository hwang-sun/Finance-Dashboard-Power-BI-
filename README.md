# AtliQ Power BI P&L Statement Report
For desktop file: https://drive.google.com/file/d/1bv0fC4aR0X6sGNlZqOEi179pn0lFi_AU/view?usp=share_link

<img src="image/finance_dashboard.PNG" align=center>
---


The report includes 6 elements:
- Key metrics include Net Sale ($), Gross Margin (%), Net Frofit (%)
- P&L Statement
- A Line chart compares P&L values between selected year and its previous year
- A bar chart compares P&L values across different customers
- A heatmap conveys how much particular products account for a specific P&L values
- Filters by fiscal years, months, and YTD, YTG

# I. Problem Context
AtliQ was established in 2017 as an IT services company to help businesses integrate their processes with automated tools. Over the past 5 years, AtliQ has successfully provided many businesses with custom solutions that help them scale, or streamline their processes, reduce overhead costs and increase overall efficiency.

The CEO of the company wants to have an dynamic report to show Profit and Loss statement to understand financial performance across markets, products, and customers.

Users are able to use the tool in their busines review meetings and in their decision making process such as customer negotiation, new product launch, marketing promotions, fnance budgeting etc.

Project Goals:
Build an interactive dashboard and pulish it onlune to unlock insights about the financial performance and enable data-driven decision making across company departments.

# II. Power Query practices
## 1. Create "fact_estimate_sale" table
In order to create P&L statement, I need to calculate the following features:

<img src="image/P&L_formula.png" width=300 height = 400 align=center>


Given the sale data only updated to 1st December 2021, It's required to join forcast data (Year-to-Go data) in  `fact_forecast_monthly` table and actual data (Year-to-Date data) in "fact_sales_monthly" to a new one called "fact_estimate_sale" table so that I'm able to conduct P&L statement and financial analysis from 2018 to the end of 2022.

Furthermore, multiple tables would need combining together so features for calculating P&L statement were available.

The following steps were performed to create `fact_estimate_sale` table:
- Calculate the last acutal sale date from *fact_sale_monthly"
```dax
last_sale_date = List.Max(fact_sales_monthly[date])
```

- Duplicate "fact_forcast_monthly" and rename it to "remain_gross_sale"
- In "remain_gross_sale" filtered out Year-to-Go rows which have *date* greater than *last_sale_date*
```dax
remain_gross_sale = Table.SelectRows(Source, each ([date] > last_sale_date)))
```

- Concat "fact_estimate_sale" and "remain_gross_sale" by rows would result in `fact_estimate_sale` table

```dax
fact_estimate_sale = Table.Combine({fact_sales_monthly, remain_gross_sale})
```

- Perform Merging "fact_estimate_sale" table to other fact tables include "fact_gross_sale", "pre_invoice_deductions" to extract *gross_price* and *pre_invoice_discount_pct* columns based on primary keys *fiscal_year*, *product_code*, and *customer_code*.

## 2. Create "P&L_structure" table for structuring P&L statement visual

<img src="image/P&L_structure.PNG" width=500 height = 400 align=center>


# III. Data Model

<img src="image/data_model.PNG" align=left>


---
The data model is formed in Star Schema with fact tables (include transactions) being put in center and dimension tables (include descriptive information) placed around.

## 1. Fact tables in data model
- fact_estimate_sale
- fact_sale_monthly
- fact_pre_invoice_deductions
- fact_post_invoice_deductions
- fact_gross_price
- fact_forecast_monthly
- fact_freight_cost
- fact_manufactoring_cost
- fact_operational_cost

## 2. Dimension tables in data model
- dim_customer
- dim_product
- dim_date

## 3. Resolve many-to-many relationship


- `linked_market` was created as a bridge table for resolving many-to-many relationship between "dim_customer" and "fact_freight_cost" table as both tables contained primary key column *market* with multiple values.

<img src="image/bridge_market.PNG" width=400 height=400 align=center>


- Similarly, `linked_fiscal_year` was created as bridge table for resolving many-to-many relationship between dimension table "dim_date" and "fact_pre_invoice_deductions", "fact_gross_price", "fact_forecast_monthly", "fact_freight_cost", "fact_manufactoring_cost", "fact_operational_cost" tables as foreign key column *fiscal_year* of those fact tables and primary key of the dimension table had multiple values.

<img src="image/bridge_fiscal_year.PNG" width=400 height=400 align=center>


# IV. DAX practices
## 1. Calculated Columns


## 2. Calculated Measures
# V. Summary
