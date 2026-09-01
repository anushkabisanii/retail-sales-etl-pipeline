# retail-sales-etl-pipeline

# Retail Sales ETL Pipeline & Data Warehouse

An end-to-end ETL pipeline that extracts raw retail transaction data, cleans and transforms it, loads it into a star-schema data warehouse, and analyzes it using SQL.

## Architecture

Raw CSV → Extract (pandas) → Transform (clean, validate, dedupe) → Load (star schema, SQLite) → SQL Analytics → Visualizations

## Dataset

Superstore Sales Dataset (Kaggle) — 51,290 retail transactions across multiple countries and regions.

## Data Model

Star schema with 1 fact table and 3 dimension tables:

- **fact_sales** (51,290 rows) — order-level transactions: Sales, Profit, Quantity, Discount, linked to all dimensions via foreign keys
- **dim_products** — product details (ID, Category, Sub-Category, Name)
- **dim_customers** (1,590 rows) — one row per unique customer, deduplicated by most recent location
- **dim_date** (1,430 rows) — date breakdown by year, month, quarter

## Data Quality

- **Customer deduplication:** raw data had 24,959 customer-location combinations for 1,590 unique customers. Resolved by keeping each customer's most recent recorded location.
- **Product ID inconsistency:** some product names map to multiple Product IDs, likely due to region-specific ID assignment. Documented as a known limitation rather than force-merged, to avoid incorrectly combining distinct products.
- **Referential integrity:** enforced via assertions that halt the pipeline if any fact_sales row references a customer, product, or date missing from its dimension table.

## Pipeline Design

- Modular functions: `extract()`, `transform()`, `build_dim_products()`, `build_dim_customers()`, `build_dim_date()`, `build_fact_sales()`, `load_to_sqlite()`
- Logging instead of print statements
- Config-driven file paths and settings
- Incremental loading demonstrated by loading historical data first, then appending new data without full reprocessing
- Error handling on data extraction

## SQL Analysis

Five queries answering business questions:
1. Revenue and profit by category
2. Top 10 customers by revenue
3. Sales trend by year and quarter
4. Revenue and profit by region
5. Average order value and profit margin by customer segment

## Key Findings

- Technology led in revenue; Office Supplies had the highest order volume
- Consistent quarter-over-quarter growth from 2011 to 2014
- Central region outperformed all other regions in both sales and profit

## Tech Stack

Python, pandas, SQLite, SQL, matplotlib

## How to Run

1. Open `Super_Store_Project.ipynb` in Google Colab
2. Upload the Superstore dataset CSV
3. Run all cells in order

## Future Improvements

- Interactive dashboard (Power BI)
- Migrate to PostgreSQL
- Chunked processing for larger-than-memory datasets
