# Pizza Sales Analysis SQL Project

## Project Overvies

**Project Title**: Pizza Sales Analysis  
**Level**: Beginner-to-Advance  
**Database**: `Pizza_hut`

## Objectives

1. ** Setup Pizza sales database**: Create and populate  Pizza Sales database with the provided sales data.
2. **Solving the Basic, Intermidiate and Advance Problems**: Handling the real world Problem of Pizzales 


## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `Pizza_hut`.
- **Table Creating and Inserting**: Four table named `orders`, `orders_details`, `pizzas`, `pizza_types`  is created to store the  data like orders, types of pizz, pizzas category, and Customer details. Each table include the different columns but some same primary key using in other table


**Creating database Pizza_hut**
```SQL
CREATE DATABASE Pizza_hut;

```

**Using the database Pizza_hut**
```
USE Pizza_hut;
```

 **Using Four taables  we have Four tables 
InseRtet using table wizard tool**
```
select * from orders;
select * from orders_details;
SELECT * FROM pizzas;
SELECT * FROM pizza_types;
```

### BASIC QUESTIONS
Q1. **retrieve the total number of order place**
```
	SELECT COUNT(order_id) FROM orders;
```



 Q2. **calculate the total prize of revenue generated from pizza sales.
join two table orders_detailes and pizzas**
```
	SELECT 
		Round(sum(orders_details.quantity * pizzas.price),2) as total_sales
	FROM 
		orders_details 
	JOIN 
		pizzas
	ON pizzas.pizza_id = orders_details.pizza_id;
```

Q3. **Identify highest prized pizza.**
```
	SELECT pizza_types.name, pizzas.price
	FROM 
		pizza_types 
	JOIN 
		pizzas
	ON pizza_types.pizza_type_id = pizzas.pizza_type_id
	ORDER BY pizzas.price desc
	LIMIT 1;
```

Q4: **Identify the most common pizza size ordered.**
```
	SELECT pizzas.size, count(orders_details.orders_details_id) as order_count
	FROM 
		pizzas 
	join 
		orders_details
	on pizzas.pizza_id = orders_details.pizza_id
	group by pizzas.size 
	order by order_count desc
	limit 1;
```

 Q4. **List the top 5 most ordered pizza types 
 along with their quantities.**
```
	SELECT pizza_types.name, sum(orders_details.quantity) as quantity
	FROM 
		pizza_types 
	join 
		pizzas
	on pizza_types.pizza_type_id = pizzas.pizza_type_id
	join 
		orders_details
	on 
		orders_details.pizza_id = pizzas.pizza_id
	group by pizza_types.name 
	order by quantity desc
	limit 5;
```
### INTERMIDIATE QUESTIONS

-- Q1.J**oin the necessary tables to find the total quantity of
each pizza category ordered.**
```
	SELECT pizza_types.category, 
		sum(orders_details.quantity) as category_quantity
	FROM 
		pizza_types
	JOIN
		pizzas
	ON  
		pizzas.pizza_type_id  = pizza_types.pizza_type_id
	JOIN 
		orders_details
	ON 
		pizzas.pizza_id = orders_details.pizza_id
	GROUP BY pizza_types.category
	ORDER BY category_quantity DESC;
```
 Q2. **Determine the distribution of orders by hour of the day.**
```
	SELECT HOUR(order_time) AS orders_hour, count(order_id)  AS orders_count
	FROM orders
	GROUP BY hour(order_time)
	ORDER BY orders_count DESC;
```

Q3. **Join relevant tables to find the category-wise 
 distribution of pizzas.**
```
	SELECT category, count(name)
	FROM pizza_types
	GROUP BY category;
```
Q4. **Group the orders by date 
and calculate the average number of pizzas ordered per day.**
```
	SELECT 
		ROUND(AVG(quantity),0) AS Average_pizzas
	FROM 
		(SELECT 
			orders.order_date, SUM(orders_details.quantity) AS quantity
		FROM 
			orders
		JOIN 
			orders_details
		ON 
		orders_details.order_id = orders.order_id
		GROUP BY orders.order_date)
	AS orders_quantity;
```
 Q5. **Determine the top 3 most ordered pizza 
types based on revenue.**
```
	SELECT pizza_types.name,
		ROUND(SUM(orders_details.quantity * pizzas.price))as Revenue
	FROM 
		pizza_types JOIN pizzas
	ON pizzas.pizza_type_id = pizza_types.pizza_type_id
	JOIN orders_details
	ON orders_details.pizza_id = orders_details.pizza_id
	GROUP BY pizza_types.name
	ORDER BY  Revenue DESC
	LIMIT 3;
 ```

### ADVANCE QUESTIONS

 Q1. ** Calculate the percentage contribution of 
 each pizza type to total revenue.**
```
	SELECT pizza_types.category, 
		ROUND(SUM(orders_details.quantity*pizzas.price)/(SELECT 
			SUM(orders_details.quantity * pizzas.price) as total_sales
			FROM 
				orders_details 
			JOIN 
				pizzas
			ON pizzas.pizza_id = orders_details.pizza_id) *100, 2) AS revenue
	FROM pizza_types JOIN pizzas
	ON pizza_types.pizza_type_id = pizzas.pizza_type_id
	JOIN orders_details
	ON orders_details.pizza_id = pizzas.pizza_id
	GROUP BY pizza_types.category ORDER BY revenue DESC;
```
   
 Q2: **Analyze the cumulative revenue generated over time.**
```
	SELECT order_date,
		ROUND(sum(revenue) OVER( ORDER BY order_date),2) AS cumu_revenue
	FROM
		(SELECT orders.order_date, 
			SUM(orders_details.quantity * pizzas.price) as revenue
		FROM orders_details 
		JOIN pizzas
		ON orders_details.pizza_id = pizzas.pizza_id
		JOIN orders
		ON orders.order_id = orders_details.order_id
		GROUP BY orders.order_date) 
	AS Sales;
```

Q3. **Determine the top 3 most ordered pizza types  based on revenue for each pizza category.**
```
	SELECT category, name, revenue
	FROM 
		(SELECT category, name, revenue, 
		RANK() OVER(PARTITION BY category order by revenue DESC) AS rn
		FROM
			(SELECT pizza_types.category, pizza_types.name,
				ROUND(sum((orders_details.quantity) * pizzas.price),2) as revenue
			FROM  pizza_types JOIN pizzas
			ON pizza_types.pizza_type_id = pizzas.pizza_type_id
			JOIN orders_details
			ON orders_details.pizza_id = pizzas.pizza_id
			GROUP BY pizza_types.category, 
			pizza_types.name
			ORDER BY revenue DESC)
		AS a)
		AS b
	WHERE rn <=3;
```



