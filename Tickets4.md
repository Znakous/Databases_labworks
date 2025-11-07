# Task 1
select distinct product_id,
	count(sales_order_id)
	over (partition by product_id)
from sales.sales_order_detail

# Task 2
select 
	det.sales_order_id,
	head.order_date,
	sum(unit_price * order_qty) over (partition by det.sales_order_id) / 
	sum(unit_price * order_qty) over (partition by extract(month from head.order_date)) * 100
from sales.sales_order_detail as det
join sales.sales_order_header as head
on det.sales_order_id = head.sales_order_id

# Task 3
select distinct
	sub.product_category_id,
	max(list_price) over (partition by sub.product_category_id) as maxx,
	first_value(pr.product_id) over (
		partition by sub.product_category_id
		order by list_price desc, pr.product_id asc) as first_id
from production.product as pr
join production.product_subcategory as sub
on pr.product_subcategory_id = sub.product_subcategory_id

# Task 4
select product_id, det.sales_order_id, order_date, unit_price, 
	unit_price - lag(unit_price, 1, 0) over (
		partition by product_id 
		order by order_date asc)
from sales.sales_order_detail as det
join sales.sales_order_header as head
on det.sales_order_id = head.sales_order_id

# Task 5
select pr.product_id, 
	count(det.sales_order_id) over (partition by det.product_id), 
	(1. * count(customer_id) over (partition by det.product_id)) /
	(count (customer_id) over (partition by product_category_id))

from sales.sales_order_detail as det
join sales.sales_order_header as head
on det.sales_order_id = head.sales_order_id
join production.product as pr
on pr.product_id = det.product_id
join production.product_subcategory as sub
on pr.product_subcategory_id = sub.product_subcategory_id


# Task 10
select customer_id, det.sales_order_id,
	sum(unit_price * order_qty) over 
		(partition by customer_id 
		order by order_date asc 
		rows between unbounded preceding and current row)

from sales.sales_order_detail as det
join sales.sales_order_header as head
on det.sales_order_id = head.sales_order_id

# Task 11
select customer_id,
	(1. * count(det.product_id) over (partition by customer_id)) / 
	count(det.product_id) over () * 100

from sales.sales_order_detail as det
join sales.sales_order_header as head
on det.sales_order_id = head.sales_order_id
