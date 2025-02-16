# Pizza Sales Analysis SQL Project

## Project Overview

**Project Title:** Pizza Sales Analysis\
**Database:** sql_project

This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, and analyze pizza sales data. The project involves setting up a pizza sales database, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries.

---

## Objectives

- **Set up a pizza sales database:** Create and populate a pizza sales database.
- **Exploratory Data Analysis (EDA):** Perform exploratory analysis to understand the dataset.
- **Business Analysis:** Use SQL queries to derive insights from the pizza sales data.

---

## Project Structure

### 1. **Database Setup**

#### **Database Creation**

```sql
CREATE DATABASE sql_project;
```

#### **Table Creation**

```sql
CREATE TABLE orders(
order_id int primary key not null,
order_date date not null,
order_time time not null
);

CREATE TABLE order_details(
order_details_id int not null,
order_id int not null,
pizza_id text not null,
quantity int not null
);
```

---

### 2. **Data Exploration**

#### **Check Total Records in Dataset**

```sql
SELECT COUNT(*) FROM pizzas;

SELECT COUNT(*) FROM pizza_types;

SELECT COUNT(*) FROM orders;

SELECT COUNT(*) FROM order_details;
```

### 3. **Basic Data Analysis Queries**

#### **Retrieve the Total Number of Orders Placed**

```sql

SELECT 
    COUNT(order_id) AS total_orders
FROM
    orders;
```

#### **Calculate the Total Revenue Generated from Pizza Sales**

```sql
SELECT 
    ROUND(SUM(order_details.quantity * pizzas.price),
            2) AS total_revenue
FROM
    order_details
        JOIN
    pizzas ON order_details.pizza_id = pizzas.pizza_id;
```

#### **Identify the Highest-Priced Pizza**

```sql
SELECT 
    pizza_types.name, pizzas.price
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY pizzas.price DESC
LIMIT 1;
```

#### **Identify the Most Common Pizza Size Ordered**

```sql
SELECT 
    pizzas.size,
    COUNT(order_details.order_details_id) AS order_ids
FROM
    pizzas
        JOIN
    order_details ON pizzas.pizza_id = order_details.pizza_id
GROUP BY pizzas.size
ORDER BY order_ids DESC
LIMIT 1;
```

#### **List the Top 5 Most Ordered Pizza Types Along with Their Quantities**

```sql
SELECT 
    pizza_types.name, SUM(order_details.quantity) AS quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY quantity DESC
LIMIT 5;
```

---

### 4. **Intermediate Data Analysis Queries**

#### **Find the Total Quantity of Each Pizza Category Ordered**

```sql
SELECT 
    pizza_types.category,
    SUM(order_details.quantity) AS category_wise_quantity
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category;
```

#### **Determine the Distribution of Orders by Hour of the Day**

```sql
SELECT 
    HOUR(order_time) AS hour_of_day,
    COUNT(order_id) AS order_count
FROM
    orders
GROUP BY hour_of_day;
```

#### **Find the Category-Wise Distribution of Pizzas Ordered**

```sql
SELECT 
    category, COUNT(name) AS pizza_type
FROM
    pizza_types
GROUP BY category;
```

#### **Calculate the Average Number of Pizzas Ordered per Day**

```sql
SELECT 
    ROUND(AVG(quantity), 0) AS avg_orders_per_day
FROM
    (SELECT 
        orders.order_date, SUM(order_details.quantity) AS quantity
    FROM
        orders
    JOIN order_details ON orders.order_id = order_details.order_id
    GROUP BY orders.order_date) AS order_quantity;
```

#### **Determine the Top 3 Most Ordered Pizza Types Based on Revenue**

```sql
SELECT 
    pizza_types.name,
    SUM(order_details.quantity * pizzas.price) AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name
ORDER BY revenue DESC
LIMIT 3;
```

#### **Identify top 2 ordering days by calculating total orders per day of the week.**

```sql
    SELECT 
    DAYNAME(orders.order_date) AS day_of_week, 
    sum(order_details.quantity) AS total_orders
FROM orders join order_details
on orders.order_id=order_details.order_id
GROUP BY day_of_week
ORDER BY total_orders DESC limit 2;
```

---

### 5. **Advanced Data Analysis Queries**

#### **Calculate the Percentage Contribution of Each Pizza Type to Total Revenue**

```sql
SELECT 
    pizza_types.category,
    ROUND(SUM(pizzas.price * order_details.quantity) / (SELECT 
                    ROUND(SUM(order_details.quantity * pizzas.price),
                                2) AS total_revenue
                FROM
                    order_details
                        JOIN
                    pizzas ON order_details.pizza_id = pizzas.pizza_id) * 100,
            2) AS revenue
FROM
    pizza_types
        JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
        JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category
ORDER BY revenue DESC;
```

#### **Analyze the Cumulative Revenue Generated Over Time**

```sql
select order_date,sum(revenue) over(order by order_date) as cum_revenue
from
(select orders.order_date,sum(order_details.quantity*pizzas.price) as revenue
from orders join order_details
on orders.order_id=order_details.order_id
join pizzas
on pizzas.pizza_id=order_details.pizza_id
group by  orders.order_date) as sales;
```

#### **Determine the Top 3 Most Ordered Pizza Types Based on Revenue for Each Category**

```sql
select category,name,ranking 
from
(select category,name,revenue,rank() over(partition by category order by revenue desc) as ranking
from
(select pizza_types.category,pizza_types.name,sum(pizzas.price*order_details.quantity) as revenue
from pizza_types join pizzas
on pizza_types.pizza_type_id=pizzas.pizza_type_id
join order_details
on order_details.pizza_id=pizzas.pizza_id
group by pizza_types.category,pizza_types.name) as sales) as new where ranking<=3;
```

---

## Findings & Insights

- **Order Trends:** Identified peak ordering hours and most popular pizza categories.
- **Revenue Insights:** Calculated revenue contribution for different pizzas.
- **Customer Preferences:** Found the top-selling pizzas and their sizes.
- **Seasonal Trends:** Analyzed revenue accumulation over time.

---

## Reports

- **Sales Summary:** Detailed report summarizing total sales, pizza types, and revenue.
- **Trend Analysis:** Insights into sales trends across different hours and days.
- **Customer Insights:** Reports on the most popular pizza sizes and categories.

---

## Conclusion

This project serves as a comprehensive introduction to SQL for data analysts, covering database setup, exploratory data analysis, and business-driven SQL queries. The insights derived can help businesses optimize their menu, identify bestsellers, and understand sales patterns.

---



