Task 1
```sql
select pr.name, cat.name from production.product as pr
join
production.product_subcategory as subcat on pr.product_subcategory_id = subcat.product_subcategory_id
join
production.product_category as cat on subcat.product_category_id = cat.product_category_id
where pr.color = 'Red' and pr.list_price >= 100
```
Task 2
```sql
select s1.name from production.product_subcategory as s1
join
production.product_subcategory as s2
on s1.name = s2.name and s1.product_subcategory_id != s2.product_subcategory_id
```
Task 3
```sql
select cat.name, count(pr.product_id) 
from production.product as pr
join
production.product_subcategory as subcat
on pr.product_subcategory_id = subcat.product_subcategory_id
join production.product_category as cat
on subcat.product_category_id = cat.product_category_id
group by cat.product_category_id
```
Task 4
```sql
select subcat.name, count(pr.product_id) 
from production.product as pr
join
production.product_subcategory as subcat
on pr.product_subcategory_id = subcat.product_subcategory_id
group by subcat.product_subcategory_id
```
Task 5
```sql
select subcat.name 
from production.product as pr
join
production.product_subcategory as subcat
on pr.product_subcategory_id = subcat.product_subcategory_id
group by subcat.product_subcategory_id
order by count(pr.product_id) desc
limit 3
```
Task 6
```sql
select subcat.name, max(pr.list_price)
from production.product as pr
join
production.product_subcategory as subcat
on pr.product_subcategory_id = subcat.product_subcategory_id
where pr.color = 'Red'
group by subcat.product_subcategory_id
```
Task 7
```sql
select v_n.name, count(v.product_id)
from purchasing.product_vendor as v
join
purchasing.vendor as v_n
on v.business_entity_id = v_n.business_entity_id
group by v_n.business_entity_id
```
Task 8
```sql
select p.name 
from production.product as p
join
purchasing.product_vendor as v
on p.product_id = v.product_id
group by p.product_id
having count(distinct v.business_entity_id) > 1
```
Task 9
```sql
select p.name 
from production.product as p
join
sales.sales_order_detail as ord
on p.product_id = ord.product_id
group by p.product_id
order by sum(ord.order_qty) desc
limit 1
```
Task 10
```sql
select cat.name 
from production.product as p
join
production.product_subcategory as subcat
on p.product_subcategory_id = subcat.product_subcategory_id
join
production.product_category as cat
on subcat.product_category_id = cat.product_category_id
join
sales.sales_order_detail as ord
on p.product_id = ord.product_id
group by cat.product_category_id
order by sum(ord.order_qty) desc
limit 1
```
Task 11
```sql
select cat.name, 
	count(distinct subcat.product_subcategory_id), 
	count(distinct p.product_id)
from production.product as p
join
production.product_subcategory as subcat
on p.product_subcategory_id = subcat.product_subcategory_id
join production.product_category as cat
on subcat.product_category_id = cat.product_category_id
join
sales.sales_order_detail as ord
on p.product_id = ord.product_id
group by cat.product_category_id
```
Task 12
```sql
select v_info.credit_rating, count(distinct v.product_id)
from purchasing.product_vendor as v
join purchasing.vendor as v_info
on v.business_entity_id = v_info.business_entity_id
group by v_info.credit_rating
```
Task 13
```sql
select p1.name from production.product as p1
join
(select distinct(p2.list_price) from production.product as p2
limit ((select count(distinct(p3.list_price)) from production.product as p3) * 0.1)) as per_select
on p1.list_price = per_select.list_price
```
Task 14
Адекватное решение
```sql
select v_info.name from (
(select v.business_entity_id 
from purchasing.product_vendor as v
group by v.business_entity_id
order by count(v.product_id)
limit 3) as best
join
purchasing.vendor as v_info
on best.business_entity_id = v_info.business_entity_id)
```
Если реально учитывать одинаковое количество товаров у разных поставщиков (типа если количества [1, 2, 3, 3, 4, 5] и вывести надо [3, 3, 4, 5], то как-то так
```sql
select v_info.name, prim.count_pr from 
((select v.business_entity_id as ent_id, 
	count(v.product_id) as count_pr
from purchasing.product_vendor as v
group by v.business_entity_id) as prim
join
(select count(p.product_id) as counts 
from purchasing.product_vendor as p
group by p.business_entity_id
order by count(p.product_id) desc
limit 3) as best_counts
on prim.count_pr = best_counts.counts
join
purchasing.vendor as v_info
on prim.ent_id = v_info.business_entity_id)
order by count_pr desc
```
Task 15
```sql
select v_info.name, 
	count(subcat.product_subcategory_id) 
from
(purchasing.product_vendor as v
join production.product as p on v.product_id = p.product_id
join 
production.product_subcategory as subcat
on p.product_subcategory_id = subcat.product_subcategory_id)
join 
purchasing.vendor as v_info 
on v.business_entity_id = v_info.business_entity_id 
group by v_info.business_entity_id
```
Task 16
```sql
select p1.name
from 
production.product as p1
join
production.product as p2
on p1.product_id != p2.product_id and p1.name = p2.name
```
