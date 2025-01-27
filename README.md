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

ROW_NUMBER: Assigns a unique number to each row, even for rows with the same sales_amount.
RANK: Tied rows receive the same rank, but the next rank has a gap.
DENSE_RANK: Similar to RANK, but there are no gaps between ranks for tied rows.
