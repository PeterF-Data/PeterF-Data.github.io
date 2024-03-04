## SQL Project for Hypothetical Pizza Delivery Business

**Project description:** I wanted to develop my SQL skills and follow a project from beginning to end. I found this project that involved creating a database and dashboard for an imaginary pizza delivery business (Ben’s Pizza) at “https://learnbi.online/pizzaproject”. The first stage of the brief involved the creation of an orders table. From the data provided by “Ben’s Pizza”, the table needed columns for: Row ID, Order ID, Item name, Item category, Item size, Item price, Quantity, Customer first name, Customer last name, Delivery address 1, Delivery address 2, Delivery city & Delivery zip code. The next stage was to create a relational database, I used “QuickDBD” for this (https://www.quickdatabasediagrams.com). Database normalisation was performed to reduce data redundancy within the database. Separate tables were created for customers, orders, address, item, recipe, ingredient, inventory, rota, shift & staff. Each table was given a unique ID and then connected via this key. This database was then exported for use in MySQL.

### 1. Main Dashboard

The project brief states that I need to create a dashboard for the following data points:

Total orders
Total sales
Total Items
Average order value
Sales by category
Top selling items
Orders by hour
Sales by hour
Orders by address
Orders by delivery/pick up
For the points above, I needed to join the orders, item & address tables. Table aliases were given (o = orders, i = item & a = address) the columns below were created, excluding the average order value, as this could be calculated later on in PowerBI. The left join command was then used to include the item_id from the item table with the orders table, and again for the add_id from the address table. 

```
SELECT
	o.order_id,
	i.item_price,
	o.quantity,
	i.item_cat,
	i.item_name,
	o.created_at,
	a.delivery_address1,
	a.delivery_address2,
	a.delivery_city,
	a.delivery_zipcode,
	o.delivery 
FROM
	orders o
	LEFT JOIN item i ON o.item_id = i.item_id
	LEFT JOIN address a ON o.add_id = a.add_id
```

### 2. Inventory Database

The second part of the brief requested that a dashboard be made for inventory management. I needed to calculate how much inventory was being used and identify inventory that needed reordering.

First, I needed to calculate the total quantity by ingredient, this could be done by taking the number of orders and then multiplying by the ingredient quantity in the recipe. I started by calculating the number of orders by pizza. I selected item_id from the orders table and item_id and sku from the item table. I used the SUM function to calculate quantity from the orders table as order_quantity. I also needed to join the item_id from the items table to the orders table via the left join command to only display items that had been ordered. Finally, I used GROUP BY to display item_id, sku & item_name in a row with the value of the SUM function as order quantity.

```
SELECT
o.item_id,
i.sku,
i.item_name,
sum(o.quantity) as order_quantity
From orders o
left join item i on o.item_id = i.item_id
group by o.item_id,i.sku,i.item_name
```

This gave the number of orders by pizza. The next stage was to break this down into pizzas by ingredients by using the recipe table. I added a LEFT JOIN to the code on sku in the items table and recipe_id from the recipe table. I needed to add columns for ing_id and quantity (as recipie_quantity) from the recipe table, so these were added to the SELECT statement. These columns also needed to be added to the GROUP BY statement. I now needed to add the ingredient names. A new LEFT JOIN was created to join ing_id from the recipe table to the ingredient table. The ing_name column was also added to both SELECT and GROUP BY. Next, I needed the columns for ingredient weight and price. This table is already joined, so I added ing.ing_weight & ing.ing_price to my SELECT & GROUP BY statements.

```
SELECT
o.item_id,
i.sku,
i.item_name,
r.ing_id,
ing.ing_name,
r.quantity as recipe_quantity,
sum(o.quantity) as order_quantity
ing.ing_weight,
ing.ing_price
From orders o
left join item i on o.item_id = i.item_id
left join recipe r on i.sku = r.recipe_id
left join ingredient ing on ing.ing_id = r.ing_id
group by 
o.item_id,
i.sku,
i.item_name,
r.ing_id,
r.quantity,
ing.ing_name,
ing.ing_weight,
ing.ing_price
```

Next, I needed to multiply order quantity by recipe quantity. For this, I had to create a sub query, as the order quantity was already within a SUM function. I did this by adding parentheses at the beginning and end of my statement, naming the query written so far as S1. Now, with a new SELECT statement, I selected the columns I needed from the s1 query and could perform my calculations. The ordered weight was calculated as order quantity * recipe quantity and unit cost as ing_price / ing_weight. To calculate the ingredient cost I now copy and pasted the two sums for ordered weight and unit cost, placed them with parentheses and * them by one another.

```
SELECT
s1.item_name,
s1.ing_id,
s1.ing_name,
s1.ing_weight,
s1.ing_price,
s1.order_quantity,
s1.recipe_quantity,
s1.order_quantity*s1.recipe_quantity as ordered_Weight,
s1.ing_price/s1.ing_weight as unit_cost,
(s1.order_quantity*s1.recipe_quantity)*(s1.ing_price/s1.ing_weight) as ingredient_cost   
FROM (SELECT
	o.item_id,
	i.sku,
	i.item_name,
	r.ing_id,
	ing.ing_name,
	r.quantity as recipe_quantity,
	SUM(o.quantity) as order_quantity,
	ing.ing_weight,
	ing.ing_price
	FROM orders o
	LEFT JOIN item i ON o.item_id = i.item_id
	LEFT JOIN recipe r ON i.sku = r.recipe_id
	LEFT JOIN ingredient ing ON ing.ing_id = r.ing_id
	GROUP BY 
	o.item_id,
	i.sku,
	i.item_name,
	r.ing_id,
	r.quantity,
	ing.ing_name,
	ing.ing_weight,
	ing.ing_price) s1;
```

### 3. Stock Management Databse

I now needed to create a database for stock management so, I created a new view named stock 1. I needed to calculate the total weight ordered, inventory amount and inventory remaining per ingredient. First, I selected ing_name and then used the SUM function to produce the ordered weight. As I used the SUM function, I also had to add GROUP BY for ing_name. This produced the ordered weight for each ingredient. Next, I needed the total weight of inventory for each ingredient which I started with. ing_id was added to GROUP BY to work as my joining key to the ingredients table. The current query was turned into a subquery called s2. The inventory table was then joined using a LEFT JOIN statement and item_id. I also needed to add the total weight in stock using ingredient quantity * ingredient weight. For this, I added ing_weight*inv.quantity as total_inv_weight below my first select statement. I also added another LEFT JOIN using ing_id from the ingredient table. Finally, I calculated the remaining weight as total_inv_weight – the ordered_weight.

```
SELECT 
	s2.ing_name,
	s2.ordered_weight,
	ing.ing_weight*inv.quantity as total_inv_weight,
	(ing.ing_weight*inv.quantity)-s2.ordered_weight as remaining_weight
from (SELECT
	ing_id,
	ing_name,
	sum(ordered_weight) as ordered_weight
	FROM
		stock1
		group by ing_name,ing_id) s2

left join inventory inv on inv.item_id = s2.ing_id
left join ingredient ing on ing.ing_id = s2.ing_id
```

### 4. Staff Management Database

The third part of the brief requested that a database be made for staff management. I selected the date from the rota table, and first name, last name and hourly rate from the staff table. I added a LEFT JOIN using staff_id from the staff table. Now, I added the start and end times from the shift table by adding them into the SELECT statement and added another LEFT JOIN using staff_id from the shift table. I needed staff cost per row, which required me to calculate the number of hours within each shift and then * by the hourly rate. I used TIMEDIFF() to produce the difference between the start and end time in minutes, and then / this by 60 to get the number of hours as a decimal to be * by hourly rate.

```
SELECT
r.date,
s.first_name,
s.last_name,
s.hourly_rate,
sh.start_time,
sh.end_time,
((hour(timediff(sh.end_time,sh.start_time))*60)+(minute(timediff(sh.end_time,sh.start_time))))/60 as hours_in_shift,
((hour(timediff(sh.end_time,sh.start_time))*60)+(minute(timediff(sh.end_time,sh.start_time))))/60*s.hourly_rate as staff_cost
from rota r
left join staff s on r.staff_id=s.staff_id
left join shift sh on r.shift_id=sh.shift_id
```

### Conclusion

This project produced a database that was able to perform all the tasks necessary for my hypothetical pizza business. In a real-life situation, I would also create a dashboard using Power BI. With this, the client would be able to have an easy overview of their business and dive deep into inventory, stock & staff management adding value to their company.    
