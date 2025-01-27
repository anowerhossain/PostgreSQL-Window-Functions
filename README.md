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
