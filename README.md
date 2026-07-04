#  Pizza Sales Analysis (SQL Project)

## Overview
The **Pizza Sales Project** is a data analysis project built around sales data from a fictional pizza restaurant. It uses **SQL** to extract, transform, and analyze order-level data in order to answer key business questions about revenue, order patterns, and customer preferences.

The goal is to provide insights and actionable information that can help a pizza restaurant optimize its operations, improve sales, and enhance customer satisfaction.

## Dataset

The project uses four related tables:

| Table | Description | Key Columns |
|---|---|---|
| `orders` | One row per order | `order_id`, `date`, `time` |
| `order_details` | Line items within each order | `order_details_id`, `order_id`, `pizza_id`, `quantity` |
| `pizzas` | Pizza SKUs (size/price variants) | `pizza_id`, `pizza_type_id`, `size`, `price` |
| `pizza_types` | Pizza names, categories, ingredients | `pizza_type_id`, `name`, `category`, `ingredients` |

**Entity relationships:**
- `orders.order_id` → `order_details.order_id`
- `order_details.pizza_id` → `pizzas.pizza_id`
- `pizzas.pizza_type_id` → `pizza_types.pizza_type_id`

## Tools Used
- **MySQL / MySQL Workbench** for querying and analysis
- **SQL** (joins, aggregations, window functions, subqueries) for data extraction and transformation

## Business Questions Answered
1. Total number of orders placed
2. Total revenue generated from pizza sales
3. The highest-priced pizza
4. The most common pizza size ordered
5. Top 5 most ordered pizza types by quantity
6. Total quantity ordered by pizza category
7. Distribution of orders by hour of the day
8. Category-wise distribution of pizza types (menu variety)
9. Average number of pizzas ordered per day
10. Top 3 most ordered pizza types by revenue
11. Percentage contribution of each pizza category to total revenue
12. Cumulative revenue generated over time
13. Top 3 pizza types by revenue within each category (using `RANK()` window function)

## Key Insights
- **21,350** total orders were placed.
- Total revenue generated: **$817,860.05**
- **The Greek Pizza** is the highest-priced item at **$35.95**.
- **Large (L)** is the most popular pizza size (18,526 orders), followed by Medium and Small.
- **The Classic Deluxe Pizza** is the top-selling pizza by quantity.
- **The Thai Chicken Pizza** generates the highest revenue among individual pizzas ($43,434.25).
- The **Classic** category contributes the largest share of revenue (~27%).
- Order volume peaks around **12–1 PM** and **5–7 PM**, aligning with lunch and dinner rushes.
- On average, about **138 pizzas** are ordered per day.

## Sample Queries

```sql
-- Total revenue generated from pizza sales
SELECT
    ROUND(SUM(order_details.quantity * pizzas.price), 2) AS total_rev_pizza_sales
FROM
    order_details
    JOIN pizzas ON pizzas.pizza_id = order_details.pizza_id;
```

```sql
-- Top 3 pizza types by revenue within each category
SELECT name, revenue
FROM (
    SELECT category, name, revenue,
           RANK() OVER (PARTITION BY category ORDER BY revenue DESC) AS rn
    FROM (
        SELECT pizza_types.category, pizza_types.name,
               SUM(order_details.quantity * pizzas.price) AS revenue
        FROM pizza_types
        JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
        GROUP BY pizza_types.category, pizza_types.name
    ) AS a
) AS b
WHERE rn <= 3;
```

## Repository Structure
```
├── orders.csv          # Order-level data (date, time)
├── order_details.csv   # Line items per order (pizza, quantity)
├── pizzas.csv           # Pizza SKUs with size and price
├── pizza_types.csv     # Pizza names, categories, ingredients
├── queries.sql          # All SQL queries used in the analysis
└── README.md
```

## Author
[kadlepremvasanth](https://github.com/kadlepremvasanth)

---
*This project uses a fictional dataset for educational/portfolio purposes.*
