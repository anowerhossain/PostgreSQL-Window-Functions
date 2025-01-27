# PostgreSQL-Window-Functions
Window function in PostgreSQL performs a calculation across a set of rows related to the current row within a query. Unlike aggregate functions, which return a single value for a group of rows, window functions return a value for every row in the query while considering the defined "window" of rows.

## Difference between ROW_NUMBER, RANK, and DENSE_RANK 🚀
- Prepare a table 
```sql
-- Create the table
CREATE TABLE sales (
    salesperson_id SERIAL PRIMARY KEY,
    region TEXT,
    sales_amount NUMERIC
);

-- Insert sample data
INSERT INTO sales (region, sales_amount) VALUES
('North', 500),
('North', 500),
('North', 400),
('South', 700),
('South', 700),
('South', 600);
```
- Using Window Functions
```sql
SELECT 
    region,
    salesperson_id,
    sales_amount,
    ROW_NUMBER() OVER (PARTITION BY region ORDER BY sales_amount DESC) AS row_num,
    RANK() OVER (PARTITION BY region ORDER BY sales_amount DESC) AS rank,
    DENSE_RANK() OVER (PARTITION BY region ORDER BY sales_amount DESC) AS dense_rank
FROM sales;
```
Results

| Region | Salesperson ID | Sales Amount | Row Number | Rank | Dense Rank |
|--------|----------------|--------------|------------|------|------------|
| North  | 1              | 500          | 1          | 1    | 1          |
| North  | 2              | 500          | 2          | 1    | 1          |
| North  | 3              | 400          | 3          | 3    | 2          |
| South  | 4              | 700          | 1          | 1    | 1          |
| South  | 5              | 700          | 2          | 1    | 1          |
| South  | 6              | 600          | 3          | 3    | 2          |


Explanation of Results:

- ROW_NUMBER: Assigns a unique number to each row, even for rows with the same sales_amount.
- RANK: Tied rows receive the same rank, but the next rank has a gap.
- DENSE_RANK: Similar to RANK, but there are no gaps between ranks for tied rows.

## LEAD and LAG Functions in PostgreSQL?
The LEAD and LAG window functions in PostgreSQL allow you to access data from the next row (LEAD) or the previous row (LAG) in the result set, without requiring a self-join. These functions are especially useful for performing comparative analysis within a dataset.

- Syntax
```sql
LEAD(column_name, [offset], [default_value]) OVER (PARTITION BY column_name ORDER BY column_name)

LAG(column_name, [offset], [default_value]) OVER (PARTITION BY column_name ORDER BY column_name)
```
- Using LEAD and LAG

```sql
SELECT 
    region,
    salesperson_id,
    sales_amount,
    LAG(sales_amount) OVER (PARTITION BY region ORDER BY sales_amount DESC) AS previous_sales,
    LEAD(sales_amount) OVER (PARTITION BY region ORDER BY sales_amount DESC) AS next_sales
FROM sales;
```
Results 

| Region | Salesperson ID | Sales Amount | Previous Sales | Next Sales |
|--------|----------------|--------------|----------------|------------|
| North  | 1              | 500          | NULL           | 400        |
| North  | 2              | 400          | 500            | 300        |
| North  | 3              | 300          | 400            | NULL       |
| South  | 4              | 700          | NULL           | 600        |
| South  | 5              | 600          | 700            | 500        |
| South  | 6              | 500          | 600            | NULL       |

Explanation

- LAG(sales_amount): Retrieves the sales_amount from the previous row within the same region (based on descending order of sales_amount). Returns NULL for the first row in each partition.
- LEAD(sales_amount): Retrieves the sales_amount from the next row within the same region. Returns NULL for the last row in each partition.

## What is FIRST_VALUE and LAST_VALUE window functions?
- FIRST_VALUE Returns the first value in a window frame. LAST_VALUE Returns the last value in a window frame. Those functions are useful to identify baseline and final values in a dataset.

```sql
SELECT 
    region,
    salesperson_id,
    sales_amount,
    FIRST_VALUE(sales_amount) OVER (PARTITION BY region ORDER BY sales_amount DESC) AS first_sale,
    LAST_VALUE(sales_amount) OVER (PARTITION BY region ORDER BY sales_amount DESC ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_sale
FROM sales;
```
Results
| Region | Salesperson ID | Sales Amount | First Sale | Last Sale |
|--------|----------------|--------------|------------|-----------|
| North  | 1              | 500          | 500        | 300       |
| North  | 2              | 400          | 500        | 300       |
| North  | 3              | 300          | 500        | 300       |
| South  | 4              | 700          | 700        | 500       |
| South  | 5              | 600          | 700        | 500       |
| South  | 6              | 500          | 700        | 500       |

Explanation

- FIRST_VALUE(sales_amount): Fetches the highest sales_amount (based on descending order) for each region.
- LAST_VALUE(sales_amount): Fetches the lowest sales_amount for each region using a complete window frame (ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING).

