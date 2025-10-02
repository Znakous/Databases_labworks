Task 1
```sql
select name, color, size from production.product
```

Task 2
```sql
select name, color, size from production.product
where list_price > 100
```

Task 3
```sql
select name, color, size from production.product
where list_price > 100 and color = 'Black'
```

Task 4
```sql
select name, color, size from production.product
where list_price > 100 and color = 'Black'
order by list_price asc
```

Task 5
```sql
select name, size from production.product
where list_price > 100 and color = 'Black'
order by list_price desc
limit 3
```

Task 6
```sql
select name, color from production.product
where color is not null and size is not null
```

Task 7
```sql
select distinct color from production.product
where list_price >= 10 and list_price <= 50
```

Task 8
```sql
select color from production.product
where name ilike 'L_N%' 
-- ilike чтобы не было регистро-зависимым
```

Task 9
```sql
select name from production.product
where (name like 'D%' or name like 'M%') and length(name) > 3 
```

Task 10
```sql
select name from production.product
where sell_start_date < '2013-01-01'
```

Task 11
```sql
select name from production.product_subcategory
```

Task 12
```sql
select name from production.product_category
```

Task 13
```sql
select first_name from person.person
where title = 'Mr.'
```

Task 14
```sql
select first_name from person.person
where title is null
```

Task 15

approach 1:
```sql
select name  from production.product
where name like '__s%' or name like '__r%'
```

approach 2:
```sql
select name  from production.product
where starts_with(substring(name from 3), 'r') or 
	  starts_with(substring(name from 3), 's')
```

Task 16
```sql
select name from production.product
where length(name) = 5
```
Task 17
```sql
select name from production.product
where sell_start_date < '2012-04-01' and sell_end_date >= '2011-03-01'
```

Task 18
```sql
select max(list_price) from production.product
where sell_start_date >= '2011-03-01'
```

Task 19
```sql
select color, count(*) from production.product
where list_price > 30
group by color
```

Task 20
```sql
select color from production.product
group by color having min(list_price) > 100
```

Task 21

approach 1:
```sql
select subcat_table.product_subcategory_id,
	(select count(*) from production.product 
	where product_subcategory_id = subcat_table.product_subcategory_id)
from production.product_subcategory as subcat_table
-- страшно выглядит, учитывает случай, когда в категории 0 товаров
```

approach 2:
```sql
select product_subcategory_id, count(*) from production.product 
where product_subcategory_id is not null
group by product_subcategory_id
order by product_subcategory_id
-- норм выглядит, но если категория с 0 товаров существует, проигнорит её
```

Task 22
```sql
select product_id, count(*) from sales.sales_order_detail
group by product_id
```

Task 23
```sql
select product_id from sales.sales_order_detail
group by product_id 
having count(*) > 5
```

Task 24
```sql
select customer_id from sales.sales_order_header
group by customer_id
having count(distinct order_date) != count(order_date)
```

Task 25
```sql
select sales_order_id from sales.sales_order_detail
group by sales_order_id
having count(distinct product_id) > 3
```

Task 26
```sql
select product_id from sales.sales_order_detail
group by product_id
having count(*) > 3 -- 1 в 1 как 23
```

Task 27
```sql
select product_id from sales.sales_order_detail
group by product_id
having count(*) = 3 or count(*) = 5
```

Task 28
```sql
select product_subcategory_id from production.product
where product_subcategory_id is not null
group by product_subcategory_id
having count(distinct product_id) > 10
```
Task 29
```sql
select product_id from sales.sales_order_detail
group by product_id
having min(order_qty) = 1 and max(order_qty) = 1
```

Task 30
```sql
select sales_order_id from sales.sales_order_detail
group by sales_order_id
order by count(distinct product_id) desc
limit 1
```

Task 31
```sql
select sales_order_id from sales.sales_order_detail
group by sales_order_id
order by sum(unit_price * order_qty) desc
limit 1
```

Task 32
```sql
select product_subcategory_id, count(product_id) from production.product
where color is not null and product_subcategory_id is not null
group by product_subcategory_id
```
Task 33
```sql
select color from production.product
where color is not null
group by color
order by count(distinct product_id) desc
```
Task 34
```sql
select product_id from sales.sales_order_detail
group by product_id
having min(order_qty) > 1 and count(*) > 2
```
