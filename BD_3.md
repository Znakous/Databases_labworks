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
```sql
with sale_info as (
    select det.sales_order_id, product_id, customer_id 
    from sales.sales_order_detail as det
    join sales.sales_order_header as head
    on det.sales_order_id = head.sales_order_id
),
from_prod as (
	select customer_id, count(distinct sales_order_id) as c from sale_info
	group by customer_id
),
from_cust_and_prod as (
	select product_id, customer_id, count(distinct sales_order_id) as c from sale_info
	group by product_id, customer_id
)
select distinct(full_.product_id) from from_cust_and_prod as full_
join from_prod as part_ on full_.customer_id = part_.customer_id
where full_.c = part_.c
```
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

## Task 8
```sql

with sale_info as (
    select det.sales_order_id, product_id, customer_id 
    from sales.sales_order_detail as det
    join sales.sales_order_header as head
    on det.sales_order_id = head.sales_order_id
),
from_prod as (
	select product_id, count(distinct customer_id) as c from sale_info
	group by product_id
)
select product_id from from_prod
where c <= 3
```

## Task 9
```sql

with sale_info as (
    select det.sales_order_id, product_id, customer_id 
    from sales.sales_order_detail as det
    join sales.sales_order_header as head
    on det.sales_order_id = head.sales_order_id
),
prod_categories as (
	select product_id, product_category_id as category, list_price
	from production.product as pr
	join production.product_subcategory as cat
	on pr.product_subcategory_id = cat.product_subcategory_id
),
max_in_cat as (
	select product_id from prod_categories as cur_cat
	where list_price = (
		select max(list_price) from prod_categories as cat
		where cat.category = cur_cat.category)
),
good_orders as (
	select distinct sales_order_id from sales.sales_order_detail as det
	where product_id = any(select product_id from max_in_cat)
),
prod_to_any as (
	select product_id as prod_id, count(distinct sales_order_id) as c 
	from sales.sales_order_detail
	group by product_id
),
prod_to_good as (
	select product_id as prod_id, count(distinct sales_order_id) as c 
	from sales.sales_order_detail
	where sales_order_id in (select sales_order_id from good_orders)
	group by product_id
)
select good_.prod_id from 
prod_to_any as any_
join prod_to_good as good_
on any_.prod_id = good_.prod_id
where any_.c = good_.c
```

## Task 10
```sql
with sale_info as (
    select det.sales_order_id, product_id, customer_id 
    from sales.sales_order_detail as det
    join sales.sales_order_header as head
    on det.sales_order_id = head.sales_order_id
),
bought_atl_4_times as (
	select product_id from sale_info
	group by product_id
	having count(distinct customer_id) >= 4
),
good_orders as (
	select sales_order_id from sale_info
	where product_id in (select product_id from bought_atl_4_times)
	group by sales_order_id
	having count(distinct product_id) >= 3
)
select customer_id from sale_info
where sales_order_id in (select sales_order_id from good_orders)
group by customer_id
having count(distinct sales_order_id) >= 2
```
## Task 11
``` sql
with sale_info as (
    select det.sales_order_id, product_id, customer_id 
    from sales.sales_order_detail as det
    join sales.sales_order_header as head
    on det.sales_order_id = head.sales_order_id
),
good_prod_cust_rel as (
	select product_id, customer_id
	from sale_info
	group by product_id, customer_id
	having count(distinct sales_order_id) = 2 -- хотя по факту надо 3, 
	-- потому что "товар был куплен дважды этим же покупателем", 
	-- то есть эта покупка + ЕЩЁ 2
),
good_prods_in_order as (
	select sales_order_id, count(distinct product_id) as c from sale_info as s1
	where exists(select 1 from good_prod_cust_rel as s2
		where s2.product_id = s1.product_id and s2.customer_id = s1.customer_id)
	group by sales_order_id
), 
prods_in_order as (
	select sales_order_id, count(distinct product_id) as c from sale_info
	group by sales_order_id
)
select good.sales_order_id
from good_prods_in_order as good
join prods_in_order as total
on good.sales_order_id = total.sales_order_id
where good.c = total.c
```

## Task 12
```sql
with sale_info as (
    select det.sales_order_id, product_id, (
		select customer_id
		from sales.sales_order_header as head
		where det.sales_order_id = head.sales_order_id
	) as customer_id
    from sales.sales_order_detail as det
)
select product_id
from sale_info as s2
group by product_id
having count(distinct customer_id) >= 3
```
## Task 13
```sql
with good_prods as (
	select product_id from sales.sales_order_detail
	group by product_id
	having count(distinct sales_order_id) > 3
)
select product_subcategory_id from production.product
where product_id in (select product_id from good_prods)
group by product_subcategory_id
having count (distinct product_id) > 3
```
## Task 14
```sql
select product_id from production.product
where product_id in (
	select product_id from sales.sales_order_detail
	group by product_id
	having count(distinct sales_order_id) <= 3
) and product_id in (
	select product_id from sales.sales_order_detail as det
	group by product_id, (
		select customer_id from sales.sales_order_header as head
		where head.sales_order_id = det.sales_order_id
		limit 1)
	having count(distinct sales_order_id) <= 1
)
```
## Task 15
```sql
select customer_id, avg(c) from
	(select (
		select customer_id from sales.sales_order_header as head
		where head.sales_order_id = det.sales_order_id), 
	count(distinct product_id) as c
	from sales.sales_order_detail as det
	group by customer_id, sales_order_id
)
group by customer_id
```
