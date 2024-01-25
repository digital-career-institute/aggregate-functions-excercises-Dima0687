# java-DB-aggregate-function

1. Select the total number of Orders.

2. Select number of orders that have ship region.

3. Select the most expensive product.

4. Select total price of order with id = 10258;

5. Select the least expensive product from order with id = 10263

6. Select all products that have price which is above the average price.

7. Calculate number of products from category Seafood.

8. Sumarize total value of products in orders made in 1996 (before discount).


```SQL
-- Select the total number of Orders.

SELECT 
	COUNT(order_id) AS total_number_of_orders
FROM orders;

-- Select number of orders that have ship region.

SELECT
	COUNT(order_id) as total_number_of_orders_with_ship_region
FROM orders
WHERE ship_region IS NOT NULL;

-- Select the most expensive product.

SELECT 
	product_name,
	unit_price
FROM products
WHERE unit_price = (
	SELECT
		MAX(unit_price)
	FROM products
);

-- Select total price of order with id = 10258;

SELECT
	order_id,
	ROUND( CAST( SUM(  unit_price * quantity * (1 - discount) )  AS NUMERIC ) , 2) AS total_price
FROM order_details
WHERE order_id = 10258
GROUP BY order_id;

-- Select the least expensive product from order with id = 10263

SELECT 
	product_name,
	unit_price
FROM products
WHERE unit_price = (
	SELECT
		MIN(unit_price)
	FROM (
		SELECT 
			p.product_name,
			p.unit_price,
			od.order_id
		FROM products p
		JOIN order_details od ON p.product_id = od.product_id
		WHERE od.order_id = 10263
	)
);

-- Select all products that have price which is above the average price.

SELECT
	p.product_name,
	p.unit_price
FROM products p
WHERE unit_price > ( 
	SELECT
		AVG(unit_price)
	FROM products
);

-- Calculate number of products from category Seafood.
SELECT
	COUNT(p.product_name) AS number_of_products,
	ct.category_name
FROM products p
RIGHT JOIN categories ct ON p.category_id = ct.category_id
WHERE ct.category_name ILIKE 'Seafood'
GROUP BY ct.category_name;

-- Sumarize total value of products in orders made in 1996 (before discount).

SELECT 
    ROUND( CAST( SUM(od.unit_price * od.quantity) AS NUMERIC ), 2 ) AS total_value
FROM 
    orders o
JOIN 
    order_details od ON o.order_id = od.order_id
WHERE 
    EXTRACT(YEAR FROM o.order_date) = 1996;
```
