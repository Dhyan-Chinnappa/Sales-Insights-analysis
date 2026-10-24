# Sales Insights: MySQL Analysis and Power BI Dashboard

An end-to-end sales analytics project. Sales data is explored and cleaned with **MySQL**, then visualised in an interactive **Power BI** dashboard that shows revenue, sales quantity, top customers, top products and market performance.

## Table of Contents
- [Project Overview](#project-overview)
- [Data Model](#data-model)
- [SQL Analysis](#sql-analysis)
- [Data Quality Notes](#data-quality-notes)
- [Power BI Dashboard](#power-bi-dashboard)
- [Repository Structure](#repository-structure)
- [Tools Used](#tools-used)
- [How to Run](#how-to-run)
- [Key Insights](#key-insights)
- [Author](#author)

## Project Overview
The goal is to answer common business questions about sales performance:
- How many customers do we have, and what do they buy?
- What is total revenue by year, by month and by market (e.g. Chennai)?
- Which customers, products and markets drive the most revenue and volume?

## Data Model
The database contains four main tables:

| Table | Description |
| --- | --- |
| `customers` | Customer records (customer code and name) |
| `transactions` | One row per sale: order date, market code, product code, currency, sales quantity, sales amount |
| `date` | Calendar table (date, year, month name) joined to `transactions.order_date` |
| `markets` | Market code and market name (e.g. `Mark001` = Chennai) |
| `products` | Product codes used in the transactions |

In Power BI the tables are loaded as `sales customers`, `sales date`, `sales markets` and `sales products`, related to the transactions data in a star schema.

## SQL Analysis
All queries are in [`analysis_using_mysql.sql`](analysis_using_mysql.sql). They cover:

| Question | Technique |
| --- | --- |
| Show all customers / total number of customers | `SELECT *`, `COUNT(*)` |
| Transactions for the Chennai market (`Mark001`) | `WHERE market_code = 'Mark001'` |
| Distinct products sold in Chennai | `SELECT DISTINCT` |
| Transactions in US dollars | `WHERE currency = 'USD'` |
| Transactions in 2020 | `INNER JOIN` on the date table |
| Total revenue in 2020 | `SUM(sales_amount)` with a join |
| Total revenue in January 2020 | Join plus filter on `month_name` |
| Total revenue in 2020 for Chennai | Join plus filter on market code |
| Currency validation checks | `DISTINCT`, `COUNT(*)` per currency |

## Data Quality Notes
- **Hidden carriage returns:** the `currency` column contains values such as `'INR\r'` and `'USD\r'` (from a Windows-format source file) as well as clean `'INR'` and `'USD'`. Filtering on `'INR'` alone silently drops rows, so the validation queries count both variants.
  Suggested fix: `UPDATE transactions SET currency = TRIM(TRAILING '\r' FROM currency);`
- **Mixed currencies:** revenue is stored in INR and USD. Summing `sales_amount` across both currencies without conversion mixes units, so normalise to one currency before reporting totals.
- **Operator precedence:** in the 2020 revenue query, `AND ... OR ...` needs brackets around the currency conditions (as in the January query) so the year filter applies to both.
- The January query currently contains a duplicated `and` keyword that should be removed.

## Power BI Dashboard
File: [`Dashboard.pbix`](Dashboard.pbix) (single page, 1920 × 1080).

| Visual | What it shows |
| --- | --- |
| Revenue card | Total revenue for the current filter selection |
| Sales quantity card | Total units sold |
| Revenue line chart | Revenue trend over time |
| Top 5 Clients | Highest-revenue customers |
| Top Products by Revenue | Revenue by product code and customer |
| Revenue by Market | Revenue per market |
| Sales Quantity by Market | Units sold per market |
| Date and Year slicers | Filter the whole page by period |

## Repository Structure
```
├── analysis_using_mysql.sql   # SQL exploration and validation queries
├── Dashboard.pbix             # Power BI dashboard
├── screenshots/               # Dashboard images (add these)
└── README.md
```

## Tools Used
- **MySQL** for querying, joins and data validation
- **Power BI Desktop** for modelling, DAX measures and visuals

## How to Run
1. Load the sales dataset into a MySQL database (tables listed above).
2. Run the queries in `analysis_using_mysql.sql` (clean the `currency` column first).
3. Open `Dashboard.pbix` in Power BI Desktop, update the data source to your MySQL server or file location, and click **Refresh**.

## Key Insights
*Add your findings here after reviewing the dashboard, for example: top 5 customers by revenue, best-performing market, and the revenue trend over time.*

## Author
*Your name* · [LinkedIn](#) · [Portfolio](#)
