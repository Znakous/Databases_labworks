Task 1
select name, color, size from production.product

Task 2
select name, color, size from production.product
where list_price > 100

Task 3
select name, color, size from production.product
where list_price > 100 and color = 'Black'

Task 4
select name, color, size from production.product
where list_price > 100 and color = 'Black'
order by list_price asc

Task 5
select name, size from production.product
where list_price > 100 and color = 'Black'
order by list_price desc
limit 3

Task 6
select name, color from production.product
where color is not null and size is not null

Task 7
select distinct color from production.product
where list_price >= 10 and list_price <= 50

Task 8
select color from production.product
where name ilike 'L_N%' 
-- ilike чтобы не было регистро-зависимым

Task 9
select name from production.product
where (name like 'D%' or name like 'M%') and length(name) > 3 


Task 10
select name from production.product
where sell_start_date < '2013-01-01'

Task 11
select name from production.product_subcategory

Task 12
select name from production.product_category

Task 13
select first_name from person.person
where title = 'Mr.'

Task 14
select first_name from person.person
where title is null

Task 15
approach 1:
select name  from production.product
where name like '__s%' or name like '__r%'

approach 2:

select name  from production.product
where starts_with(substring(name from 3), 'r') or 
	  starts_with(substring(name from 3), 's')

Task 16
select name from production.product
where length(name) = 5

Task 17
select name from production.product
where sell_start_date < '2012-04-01' and sell_end_date >= '2011-03-01'

Task 18
select max(list_price) from production.product
where sell_start_date >= '2011-03-01'

Task 19
select color, count(*) from production.product
where list_price > 30
group by color

Task 20
select color from production.product
group by color having min(list_price) > 100

Task 21
approach 1:
select subcat_table.product_subcategory_id,
	(select count(*) from production.product 
	where product_subcategory_id = subcat_table.product_subcategory_id)
from production.product_subcategory as subcat_table
-- страшно выглядит, учитывает случай, когда в категории 0 товаров

approach 2:
select product_subcategory_id, count(*) from production.product 
where product_subcategory_id is not null
group by product_subcategory_id
order by product_subcategory_id
-- норм выглядит, но если категория с 0 товаров существует, проигнорит её

Task 22
select product_id, count(*) from sales.sales_order_detail
group by product_id

Task 23
select product_id from sales.sales_order_detail
group by product_id 
having count(*) > 5

Task 24
select customer_id from sales.sales_order_header
group by customer_id
having count(distinct order_date) != count(order_date)

Task 25
select sales_order_id from sales.sales_order_detail
group by sales_order_id
having count(distinct product_id) > 3

Tsk 26
select product_id from sales.sales_order_detail
group by product_id
having count(*) > 3 -- 1 в 1 как 23

Task 27
select product_id from sales.sales_order_detail
group by product_id
having count(*) = 3 or count(*) = 5

Task 28
select product_subcategory_id from production.product
where product_subcategory_id is not null
group by product_subcategory_id
having count(distinct product_id) > 10

Task 29
select product_id from sales.sales_order_detail
group by product_id
having min(order_qty) = 1 and max(order_qty) = 1

Task 30
select sales_order_id from sales.sales_order_detail
group by sales_order_id
order by count(distinct product_id) desc
limit 1

Task 31
select sales_order_id from sales.sales_order_detail
group by sales_order_id
order by sum(unit_price * order_qty) desc
limit 1

Task 32
select product_subcategory_id, count(product_id) from production.product
where color is not null and product_subcategory_id is not null
group by product_subcategory_id

Task 33
select color from production.product
where color is not null
group by color
order by count(distinct product_id) desc

Task 34
select product_id from sales.sales_order_detail
group by product_id
having min(order_qty) > 1 and count(*) > 2