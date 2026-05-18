The Problem: Coffee Shop Sales Analysis
Imagine you have two CSV files for a small chain of coffee shops:
transactions.csv — one row per item sold

transaction_id, date (YYYY-MM-DD), store_id, product_id, quantity, unit_price

products.csv — product catalog

product_id, product_name, category (e.g. "Coffee", "Tea", "Pastry"), cost (what the shop pays per unit)

You can either generate fake data yourself with a small script, or grab a similar public dataset (Kaggle has a "Coffee Shop Sales" dataset that fits this shape closely).
Tasks, roughly easy → harder

Load & inspect. Read both CSVs. Check shape, dtypes, nulls, and a few sample rows. Confirm date is parsed as a datetime, not a string.
Revenue per transaction. Add a revenue column (quantity × unit_price). What's the total revenue across the dataset?
Top sellers. Which 5 products generated the most revenue? Which 5 sold the most units? (They may not be the same.)
Category profit. Join the two tables. Compute profit per row (revenue − quantity × cost), then total profit per category. Which category is most profitable overall vs. most profitable per unit sold?
Time patterns. Group by day of week and by hour-of-day (you'll need to add an hour column — assume the date includes a time, or invent one). When are the busiest sales hours? Does the answer change by category?
Store comparison. For each store, compute monthly revenue. Reshape into a table where rows are months and columns are stores. Which store has the most consistent month-over-month growth?
Stretch. Identify the "signature product" of each store — the product whose share of that store's revenue is the most disproportionately high compared to its share across the whole chain.

Hints (concepts, not code)

For task 4, think about which kind of join you need and what the key column is.
Task 5's hour-of-day extraction lives on the .dt accessor of a datetime Series.
Task 6 is where pivot_table shines — figure out what goes in index, columns, values, and aggfunc.
Task 7 is essentially: per-store product share ÷ overall product share, then take the max per store.