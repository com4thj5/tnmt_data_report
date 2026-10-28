# TNMT Retail — Sales Dashboard 

## Project Overview
<img width="1029" height="690" alt="Screenshot 2025-11-26 093031" src="https://github.com/user-attachments/assets/4af8704d-2fd8-4689-8562-ca1a75110913" />

*TNMT Retail* sales director requested a set of calculations, reports and a dashboard built from the supplied sales dataset. The goal is to produce clear, actionable visualizations and summary insights for revenue, profit, product categories, sales teams and state-level performance — including relationships with population and median income.

---

## Dataset (expected fields)

> If your dataset field names differ, update the field names in the steps below accordingly

Typical fields used in this project:

* OrderID (unique per order)
* OrderDate (YYYY-MM-DD)
* ProductID
* ProductName
* ProductCategory (e.g., Decoratives, Furniture, Lighting, etc.)
* UnitsSold or Quantity
* UnitPrice (selling price per unit)
* UnitCost or Cost (cost per unit)
* SalesTeam (or SalesPerson / Team)
* State (customer state)
* City (optional)
* Other fields (promotions, channel, etc.)

Additional external data (for state-level analysis):

* StatePopulation (population per state, for the relevant year)
* StateMedianIncome (median household income per state, same year ideally)

---

## Calculations (per order)


Compute the following *for every order / order line*:

1. *Revenue* = UnitsSold * UnitPrice
2. *Cost* = UnitsSold * UnitCost
3. *Profit* = Revenue - Cost
4. *Profit Margin (%)* = Profit / Revenue * 100 (handle zero revenue safely)
5. *Order Month* and *Order Year* — extract month name and year from OrderDate (used for monthly comparisons)
6. *Category Contribution Flag* (optional) — a small indicator if the product belongs to a target category (eg IsDecorative)

*SQL example (per order line):*

sql
SELECT
  OrderID,
  ProductID,
  ProductName,
  ProductCategory,
  UnitsSold,
  UnitPrice,
  UnitCost,
  UnitsSold * UnitPrice AS Revenue,
  UnitsSold * UnitCost  AS Cost,
  (UnitsSold * UnitPrice) - (UnitsSold * UnitCost) AS Profit,
  CASE WHEN UnitsSold * UnitPrice = 0 THEN 0
       ELSE ((UnitsSold * UnitPrice) - (UnitsSold * UnitCost)) / (UnitsSold * UnitPrice) * 100
  END AS ProfitMarginPct,
  DATE_TRUNC('month', OrderDate) AS OrderMonth
FROM sales_table;


*Pandas (Python) example:*

python
df['Revenue'] = df['Quantity'] * df['UnitPrice']
df['Cost'] = df['Quantity'] * df['UnitCost']
df['Profit'] = df['Revenue'] - df['Cost']
df['ProfitMarginPct'] = df.apply(lambda r: (r['Profit']/r['Revenue']*100) if r['Revenue']!=0 else 0, axis=1)
df['OrderMonth'] = pd.to_datetime(df['OrderDate']).dt.to_period('M')


---

## Required Reports & Dashboard Elements

Create a dashboard (Power BI recommended; alternative: Tableau or a Python dashboard with Plotly Dash/Streamlit) containing the following reports and interactive elements.<img width="1185" height="651" alt="Screenshot 2025-11-24 155810" src="https://github.com/user-attachments/assets/d2dde0a4-df84-442e-885d-773ad8da2626" />


### A. Slicers / Filters (global)

* Date range (OrderDate)
* Product Category
* Sales Team
* State
* Product (searchable)


### B. Key visuals / reports

1. *Revenue KPI (card)* — total revenue for the selected period. Make this visual sliceable by:

   * *Profit* (e.g., show revenue and profit side-by-side or allow toggling to view profit figure)
   * *Product Category* (use Category slicer)

2. *Profit KPI (card)* — total profit and profit margin.

3. *Revenue trend (time series)* — monthly revenue line chart (include month-level granularity). This will also be used for insight textbox "revenue trend".

4. *Top products by revenue (bar chart)* — show top N products (default top 10). Provide an option to filter to a single month (e.g., January) to answer: Which are the 2 top revenue products in January?

5. *Products by revenue (treemap or bar)* — category or product-level revenue breakdown.

6. *Sales team by revenue (bar/horizontal bar)* — shows which sales teams produced the most revenue. Add a filter to show only ProductCategory = 'Decoratives' to answer: Which sales team made most revenue from Decoratives?

7. *Revenue contribution (%) by product category (pie or stacked bar)* — include March filter to display revenue contribution (%) of the product categories in March.

8. *States by revenue (map or bar)* — show state revenue and include columns for Population and MedianIncome. Use a tooltip or a small table next to the map showing: Revenue, Population, Median Income, Revenue per capita.

9. *Relationship between revenue, population and median income (scatter plot)* — each point = state; x-axis: Population (or MedianIncome), y-axis: Revenue, point size: MedianIncome or Revenue per capita. Include a trendline and correlation coefficient. This answers: What is the relationship between revenue, population and median income per state?

### C. Layout suggestion (Power BI)

* Top row: KPI cards (Revenue, Profit, Profit margin, #Orders)
* Middle left: Revenue trend line (monthly)
* Middle right: Top products by revenue and products-by-revenue treemap
* Lower left: Sales team by revenue (with category filter control)
* Lower middle: Revenue by state (map) + small table with population & median income
* Lower right: Scatter plot (Revenue vs Population / Median Income)
* Always visible: Slicers pane and a textbox (see next section)

---

## Insights textbox (pre-written items to highlight)
<img width="995" height="649" alt="Screenshot 2025-11-26 101656" src="https://github.com/user-attachments/assets/4f20b9c2-964a-4db1-b1f0-184f0dc13e46" />

Place a textbox component on the dashboard (top or side) containing the following dynamic or static insights. If dynamic, use DAX measures or Python to compute the statements.

1. *Revenue trend:* "Revenue increased/decreased by X% from [start-month] to [end-month]. The highest month was [Month YYYY] with ₦Y revenue, and the lowest was [Month YYYY] with ₦Z revenue."

   * DAX example to compute percentage change month-over-month: DIVIDE([Revenue] - CALCULATE([Revenue], DATEADD('Calendar'[Date], -1, MONTH)), CALCULATE([Revenue], DATEADD('Calendar'[Date], -1, MONTH)))

2. *Which are the 2 top revenue products in January?*

   * Create a measure filtered to January and return TOPN(2, VALUES(Products[ProductName]), [Revenue], DESC).
   * Example (Power BI): use a Top N filter or a DAX table TOPN(2, FILTER(ALL(Products), MONTH(Products[OrderDate]) = 1), [Revenue]) and display names.

3. *Products by revenue:* short statement summarizing top 3 categories and their % shares.

4. *Which sales team made most revenue from Decoratives?*

   * Add a measure: CALCULATE([Revenue], ProductCategory = "Decoratives") grouped by SalesTeam. Show top result.

5. *Revenue contribution (%) of product categories in March:*

   * Filter OrderMonth = March and compute each Category's Revenue / CALCULATE([Revenue], ALL(ProductCategory)).

6. *Sales team by revenue:* short sentence calling out the top 1-2 performing teams and growth vs prior period (if available).

7. *Relationship between revenue, population and median income per state:*

   * Report the Pearson correlation coefficients: corr(revenue, population) and corr(revenue, median_income) and a short interpretation e.g., "Revenue correlates moderately with population (r = 0.52) but weakly with median income (r = 0.18)."

8. *States by revenue, also showing population and median income:*

   * Provide a small table with top 5 states by revenue and columns: Revenue, Population, MedianIncome, Revenue per Capita.

---

## Suggested DAX / SQL / Python snippets (quick reference)

*Power BI: Total Revenue measure (DAX)*

dax
TotalRevenue = SUMX(Sales, Sales[Quantity] * Sales[UnitPrice])
TotalProfit = SUMX(Sales, (Sales[Quantity] * Sales[UnitPrice]) - (Sales[Quantity]*Sales[UnitCost]))


*Top 2 products in January (DAX table measure)*

dax
Top2JanProducts =
TOPN(
  2,
  FILTER(ALL(Sales), MONTH(Sales[OrderDate]) = 1),
  [TotalRevenue],
  DESC
)


*Revenue contribution in March (DAX)*

dax
Revenue_March = CALCULATE([TotalRevenue], FILTER(ALL(Sales), MONTH(Sales[OrderDate]) = 3))
RevenueByCategory_MarchPct = DIVIDE([TotalRevenue], CALCULATE([TotalRevenue], ALL(Product[Category])), 0)


*Correlation (Python / pandas)*

python
import pandas as pd
import numpy as np

agg = sales.groupby('State').agg({'Revenue':'sum'}).reset_index()
# join population and median income
agg = agg.merge(pop_df, on='State').merge(income_df, on='State')

corr_pop = agg['Revenue'].corr(agg['Population'])
corr_income = agg['Revenue'].corr(agg['MedianIncome'])


---

## Deliverables

1. Power BI .pbix dashboard file (recommended)
2. A PDF / PNG export of the dashboard pages
3. CSV files used and final preprocessed dataset
4. Short one-page insights summary (can be auto-generated from the textbox)

---

## File & folder structure (recommended)


/TNMT-Retail-Analysis
  /data
    sales_raw.csv
    sales_clean.csv
    state_population.csv
    state_median_income.csv
  /notebooks<img width="1185" height="651" alt="Screenshot 2025-11-24 155810" src="https://github.com/user-attachments/assets/8b8ef8df-d1a2-4081-b549-d5c4dfe77d8b" />

    01_data_cleaning.ipynb
    02_analysis.ipynb
  /powerbi
    TNMT_Retail_Dashboard.pbix
  /reports
    TNMT_Dashboard_Export.pdf
  README.md<img width="1185" height="651" alt="Screenshot 2025-11-24 155810" src="https://github.com/user-attachments/assets/76ff1e7c-f8ca-4685-9501-37dbc9ba669b" />
