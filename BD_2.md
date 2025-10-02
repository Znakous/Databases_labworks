Task 1
```
select pr.name, cat.name from production.product as pr
join
production.product_subcategory as subcat on pr.product_subcategory_id = subcat.product_subcategory_id
join
production.product_category as cat on subcat.product_category_id = cat.product_category_id
where pr.color = 'Red' and pr.list_price >= 100
```
Task 2
```
select s1.name from production.product_subcategory as s1
join
production.product_subcategory as s2
on s1.name = s2.name and s1.product_subcategory_id != s2.product_subcategory_id
```
Task 3
```
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
```
select subcat.name, count(pr.product_id) 
from production.product as pr
join
production.product_subcategory as subcat
on pr.product_subcategory_id = subcat.product_subcategory_id
group by subcat.product_subcategory_id
```
Task 5
```
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
```
select subcat.name, max(pr.list_price)
from production.product as pr
join
production.product_subcategory as subcat
on pr.product_subcategory_id = subcat.product_subcategory_id
where pr.color = 'Red'
group by subcat.product_subcategory_id
```
...
