# Pizza Place Sales Analysis
A SQL and Tableau project analyzing pizza restaurant sales in 2015.
# Project Objective
The goal of this project is to analyze pizza restaurant sales, identify the best-performing categories and menu items, understand peak demand hours, and develop actionable recommendations for the business owner.
# Key Business Questions

What was the total revenue, and how many orders were placed?
How did revenue and the number of orders change month by month?
Which pizza categories generated the most revenue?
Which pizza size accounted for the largest share of revenue?
Which pizzas were the top and bottom performers in terms of revenue?
During which hours was revenue the highest and lowest?
How was customer demand distributed across days of the week and hours of the day?
What operational and marketing decisions can be recommended based on the findings?

# Data
The project uses the Pizza Place Sales dataset, which contains sales records for a pizza restaurant in New Jersey from January through December 2015.
The database consists of four related tables:
orders — order ID, order date, and time.
order_details — order line items, order IDs, pizza IDs, and quantities.
pizzas — pizza ID, type, size, and price.
pizza_types — pizza name, category, and ingredients.
The tables are joined using the order_id, pizza_id, and pizza_type_id keys. The order_details table links orders to specific pizzas.
Dataset Overview and Key Metrics
Period: 2015
Total orders: 21,350
Rows in order_details (order line items): 48,620
Records in pizzas (pizza varieties by size): 96
Pizza types in pizza_types: 32
Total pizzas sold: 49,574
Total revenue: $817,860
Average order value: $38.31
Revenue Calculation
Revenue is calculated as the sum of the quantities sold multiplied by the corresponding pizza prices:
SUM(od.quantity * p.price) AS revenue
For the monthly analysis, the orders, order_details, and pizzas tables are joined using JOIN, and the results are grouped by month.
# Key Questions and Findings
1. What was the total revenue?
Total revenue for 2015 was $817,860.
2. How many orders were placed?
A total of 21,350 orders were placed during the year.
3. What was the average order value?
The average order value was approximately $38.31.
4. Which pizza categories generated the most revenue?
The Classic category ranked first, followed by Supreme, Chicken, and Veggie.
Classic: $220,053
Supreme: $208,197
Chicken: $195,920
Veggie: $193,690
5. Which pizza size generated the most revenue?
Large (L) pizzas generated the most revenue, at approximately $375,319, accounting for around 46% of total revenue.
This reflects revenue performance and does not, by itself, establish that Large pizzas are the most profitable.
6. Which pizzas were the top and bottom performers in terms of revenue?
Among the leading performers were:
The Thai Chicken Pizza: $43,434.25
The Barbecue Chicken Pizza: $42,768.00
The Sicilian Pizza generated the lowest revenue among the items shown in the ranking, at $30,940.50.
7. When was demand the highest?
The highest sales activity occurred approximately between 12:00 PM and 1:00 PM and 5:00 PM and 7:00 PM. Revenue declined noticeably after 9:00 PM.
8. How did sales vary throughout the year and across the days of the week?
Monthly performance was relatively stable overall, according to the presentation. Revenue and order volume peaked in July, while the lowest levels were observed in September and October.
The heatmap revealed recurring demand peaks during lunch and evening hours throughout the week.

# Tools and Technologies
SQL / SQLite — joining tables, aggregating data, and calculating key metrics.
Tableau — creating interactive dashboards and visualizing results.
Three dashboards were developed in Tableau:
Executive Sales Overview — key performance indicators (KPIs) and monthly revenue and order trends.
Pizza Portfolio Analysis — revenue by category and size, plus the top 10 and bottom 10 pizzas by revenue.
Customer Demand Patterns — hourly revenue and a day-of-week-by-hour heatmap.

# Business Recommendations

1. Optimize Staff Scheduling
Ensure sufficient staffing during peak demand periods, particularly from 12:00 PM to 1:00 PM and from 5:00 PM to 7:00 PM. Prepare ingredients in advance to improve operational efficiency during busy hours.
2. Promote High-Performing Menu Items
Focus promotional efforts on The Thai Chicken Pizza, The Barbecue Chicken Pizza, and Large pizzas, which generated high revenue.
3. Test an Evening Promotion
Experiment with a promotion after 9:00 PM, such as a discount or a combo offer.
Measure the results of the test to determine its effectiveness. The existing data alone does not demonstrate that such a promotion would increase revenue or profit.

# Limitations and Next Steps
The dataset covers only one year, so long-term trends cannot be confirmed.
Customer demographic data is not available.
Marketing campaign data is not available.
Cost data is missing, so revenue cannot be equated with profit.
The impact of the proposed promotions has not been experimentally tested.
Next Steps
Add data from multiple years to identify and validate long-term trends.
Incorporate product costs to calculate margins and profitability by menu item.
Run an evening promotion experiment and compare the results with a baseline period.
Where possible, incorporate marketing activity and sales channel data.

# Project Links

Tableau Public Dashboard: https://public.tableau.com/views/pizza_dashboard_17907870066710/CustomerDemandPatterns?:language=de-DE&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link

Project Presentation: https://github.com/LiliiaKuchvara/pizza-sales-analysis/tree/028d7e39f73589fd43736f00b733d80f5ef84cb0/presentation

SQL Queries: https://github.com/LiliiaKuchvara/pizza-sales-analysis/blob/028d7e39f73589fd43736f00b733d80f5ef84cb0/sql/tasks

Dataset: https://github.com/LiliiaKuchvara/pizza-sales-analysis/tree/028d7e39f73589fd43736f00b733d80f5ef84cb0/date
