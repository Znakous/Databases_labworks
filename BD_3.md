## Task 1
```sql
select name from production.product
where product_id in (
	select product_id from sales.sales_order_detail
	group by product_id
	having count(*) = (
		select max(count_pr) from (
			select count(*) as count_pr from sales.sales_order_detail
			group by product_id
		)
	)
	
)
```
## Task 2
```sql
select customer_id from sales.sales_order_header
where sales_order_id = (
	select sales_order_id from sales.sales_order_detail
	where order_qty * unit_price = (
		select max(order_qty * unit_price) from sales.sales_order_detail
	)
)
```
## Task 3
```sql
select name from production.product
where product_id in (
	select product_id from sales.sales_order_detail as det
	group by product_id
	having count(distinct (
		select customer_id from sales.sales_order_header as head
		where head.sales_order_id = det.sales_order_id
	)) = 1
)
```
## Task 4
```sql
select name from production.product as p1
where list_price > (
	select avg(list_price) from production.product as p2
	where p1.product_subcategory_id = p2.product_subcategory_id
)
```
## Task 5

Там 2 трактовки разных и мне не одна не нравится :(

Я отказываюсь это решать

## Task 6

## Task 7
```sql
with sale_info as (
    select det.sales_order_id, product_id, customer_id 
    from sales.sales_order_detail as det
    join sales.sales_order_header as head
    on det.sales_order_id = head.sales_order_id
),
from_cust as (
	select customer_id, count(distinct sales_order_id) as c from sale_info
	group by customer_id
),
from_cust_and_prod as (
	select customer_id, count(distinct sales_order_id) as c from sale_info
	group by customer_id, product_id
)
select distinct(full_.customer_id) from from_cust_and_prod as full_
join from_cust as part_ on full_.customer_id = part_.customer_id
where full_.c = part_.c
```
