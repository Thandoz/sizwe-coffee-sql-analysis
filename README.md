# Sizwe Coffee Sales & Performance — SQL Analysis Project

📌 Project Overview
This project uses SQL to analyse business performance for **Sizwe Coffee (Pty) Ltd**, a coffee roastery and wholesale supplier based in Isando, Johannesburg, with a second distribution centre in Epping, Cape Town. The company sells coffee products to approximately 1,400 business customers — including grocery chains, restaurants, cafés, hotels, and offices — through two sales channels: **Wholesale** (70% of revenue) and **Subscription** (30% of revenue).

The financial year runs from **1 July to 30 June**. This analysis covers the period **1 July 2023 to 30 June 2025**, comparing performance between the 2024 and 2025 financial years. Eight business questions were answered to support the company’s year-end review, covering revenue growth, profitability, collections, key customers, sales performance, seasonality, and margin trends.

**Key business figures from the period:**
- FY2024 total revenue: R441 million
- FY2025 total revenue: R480 million (~9% growth)
- Gross margin declined from 34.7% to 33.0% despite revenue growth
- Team size: 320 staff
- Sales representatives: 18 across multiple territories

---

## 🗄️ Dataset & Tables Used
All data comes from the company’s operational database, consisting of 8 tables:

| Table Name | Number of Rows | What It Contains |
|---|---|---|
| `customers` | 1,400 | One record per customer — trading name, channel, business segment, province, assigned sales representative, and credit limit |
| `products` | 23 | Product catalogue — product line name, pack size, list price, and unit cost |
| `sales_orders` | 48,144 | Order records — customer reference, order date, delivery date, sales channel, and responsible sales representative |
| `sales_lines` | 168,172 | Individual line items within each order — product code, quantity sold, unit price, discount applied, line revenue, and cost of goods. **Revenue is calculated from this table, not from `sales_orders`** |
| `ar_ledger` | 48,144 | Customer invoices — amount charged, due date, amount settled, and settlement date |
| `reps` | 18 | Sales representative details — name, territory, and monthly revenue target |
| `rep_visits` | 9,400 | Records of customer visits made by sales representatives, including visit outcome |
| `trade_promotions` | 640 | Special pricing or promotional support provided to specific customers on specific products |

### How Tables Connect
Tables are linked using shared identifier columns as follows:
- `sales_orders.customer_id` ↔ `customers.customer_id`
- `sales_orders.order_id` ↔ `sales_lines.order_id`
- `sales_lines.sku` ↔ `products.sku`
- `customers.rep_id` ↔ `reps.rep_id`
- `ar_ledger.customer_id` ↔ `customers.customer_id`
- `rep_visits.customer_id` ↔ `customers.customer_id`
- `trade_promotions.customer_id` ↔ `customers.customer_id`

> **Note:** The `channel` field appears in both `customers` and `sales_orders`. The order-level channel from `sales_orders` was used where date-specific analysis was required, as it reflects the channel at the time of each transaction.

---

## 🛠️ Tools & Techniques Used
| SQL / Analytical Technique | Purpose | Applied In Task |
|---|---|---|
| Filtering by date ranges | Separate data into financial years | All tasks |
| Conditional aggregation (`SUM(CASE WHEN...)`) | Show FY2024 and FY2025 revenue side-by-side in one table | Task 1 |
| Basic joins (`INNER JOIN`) | Combine revenue data with product names and customer details | Tasks 1, 2, 3, 8 |
| Left joins (`LEFT JOIN`) | Include customers with no assigned representative and reps with zero revenue | Tasks 4, 6 |
| Grouping and aggregation (`GROUP BY`, `SUM`, `COUNT`, `AVG`) | Calculate totals, counts, and averages per category | All tasks |
| Percentage calculations and rounding | Growth rates, margins, and revenue share analysis | All tasks |
| CTEs (`WITH` clauses) | Create intermediate result sets for cleaner, readable queries | Tasks 5, 7, 8 |
| Window functions (`LAG()`, `OVER()`) | Month-on-month change and rolling average calculations | Task 7 |
| Exclusion logic (`IS NULL`) | Identify customers who have never received promotions | Task 5 |
| Mix/composition analysis | Compare product performance across years to explain margin change | Task 8 |

---

## 📋 Questions Answered
The following 8 specific business questions were addressed:

### Task 1 — Channel Performance
Show revenue for each sales channel in FY2024 and FY2025, plus the percentage growth between years.
- **FY2024 defined as:** Orders placed on or before 30 June 2024
- **FY2025 defined as:** Orders placed after 30 June 2024

### Task 2 — Profit by Product Line
For each product line, calculate total revenue, total cost of goods, gross profit, and gross margin percentage. Results sorted by gross profit from highest to lowest. Identifies which product lines drive the most profit versus which deliver the highest margin percentage.

### Task 3 — Collections & Payment Trends
For each channel, show total number of invoices, number fully settled, number still open, average days taken to pay, and total outstanding amount. Highlights where slow payment is most affecting cash flow.

### Task 4 — Top 10 Customers
List the ten highest-revenue customers for FY2025, including trading name, segment, province, sales representative, and total revenue. Customers without an assigned representative are still included in results.

### Task 5 — Promotion Gap
Identify the fifteen highest-revenue Wholesale customers who have never received any trade promotion support. Recommends immediate engagement with the top three accounts on the list.

### Task 6 — Sales Representative Performance
Compare every sales representative’s actual average monthly revenue against their monthly target for FY2025. Results include representatives who generated no revenue during the period, sorted by performance percentage from highest to lowest.

### Task 7 — Monthly Trends & Seasonality
Show total revenue per month across the full two-year period, including month-on-month percentage change and a rolling three-month average. Identifies seasonal patterns and the quietest trading month of the year.

### Task 8 — Margin Decline Analysis
Investigates why overall gross margin fell from 34.7% in FY2024 to 33.0% in FY2025 despite nearly 9% revenue growth. Compares revenue, margin percentage, and revenue share for each product line across both years to determine the cause.

---

## 🚀 How to Run This Project
### Requirements
- Database: PostgreSQL-compatible environment (also runs successfully in Databricks SQL and DuckDB)
- Source data: All 8 tables loaded and accessible in your database/schema

### Steps
1. Open the file named **`queries.sql`** in your SQL editor
2. If your tables are not in your default schema, add the appropriate catalog/schema prefix to table names or run:
   ```sql
   USE CATALOG your_catalog_name;
   USE SCHEMA your_schema_name;
