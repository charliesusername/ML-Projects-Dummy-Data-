The Problem: Customer Cohort & Retention Analysis
You'll work with synthetic e-commerce data and build a cohort retention analysis — the kind of thing analytics teams use to answer "are customers we acquired in March still buying from us 6 months later?"
I'll generate the data for you. Two files:
customers.csv — customer_id, signup_date, acquisition_channel (organic / paid_search / social / referral), country
orders.csv — order_id, customer_id, order_date, order_value, num_items
Let me know when you want the data and I'll produce it. In the meantime, here are the tasks.
Tasks (easy → hard)
1. Data hygiene. Load both files. Check that every customer_id in orders exists in customers. Find any orders dated before their customer's signup date — these are data quality bugs you'll need to decide how to handle. How many duplicate orders are there (same customer, same date, same value)?
2. Customer lifetime value (basic). For each customer, compute total spend, order count, average order value, and days between first and last order. What's the distribution look like? (Mean vs. median — which one is misleading and why?)
3. Cohort assignment. Assign each customer to a "signup cohort" = the year-month they signed up (e.g. 2024-03). Then compute, for each cohort: how many customers, total revenue, and average revenue per customer.
4. The cohort retention matrix. This is the meat of the problem. Build a table where:

Rows are signup cohorts (e.g. 2024-01, 2024-02, ...)
Columns are "months since signup" (0, 1, 2, 3, ...)
Each cell is the percentage of that cohort that placed an order in that month-since-signup

Month 0 should be 100% (everyone orders in their signup month, by definition — or close to it). The diagonal should taper as cohorts age. Older cohorts will have data for more months; younger cohorts will have NaN for future months they haven't reached yet.
This is a classic triangular cohort table. Getting the month-since-signup calculation right is the main challenge.
5. Channel effectiveness. Which acquisition channel produces customers with the highest 90-day LTV? Don't just compare means — sample sizes vary by channel, and a channel with 10 whales can beat one with 1000 steady customers on the mean. Show the median too, and the count.
6. Stretch: churn definition. Define "churned" as: a customer whose most recent order was more than 90 days before the dataset's max date. What's the churn rate by cohort? By channel? Does churn correlate with first-order value (i.e., do customers with bigger first orders stick around longer)?
Skills this exercises
pd.to_datetime and date arithmetic, dt.to_period, multi-level groupby, pivot_table with custom aggfuncs, .transform() (you'll likely want it for "first order date per customer"), merging on customer_id, nunique() for unique customer counts, and reasoning about distributions vs. summary stats.
The retention matrix in particular is something you'll see in real analytics work — it's worth getting fluent with.