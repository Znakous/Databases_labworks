## Task 1
Найти долю продаж каждого продукта (цена продукта × количество продукта) по каждому чеку, в денежном выражении.
```sql
select sales_order_id,
		product_id,
		cast(unit_price * order_qty / 
			sum(unit_price * order_qty) over (partition by sales_order_id)
			* 100 as decimal(5, 2))
from sales.sales_order_detail
```
## Task 2
Вывести список продуктов, их стоимость, а также разницу между стоимостью этого продукта и стоимостью самого дешевого продукта в той же подкатегории, к которой относится продукт 53.
```sql
select 
    product_id,
    name,
    list_price,
    list_price - (select min(list_price) over(
        partition by product_subcategory_id
    )
	where product_subcategory_id = (
    select first_value(product_subcategory_id) over (partition by product_subcategory_id)
    from production.product
    where product_id = 53 -- такого не существует, лол
)) as price_difference
from production.product

```
## Task 3
Вывести три колонки: номер покупателя, номер чека покупателя (отсортированный по возрастанию даты чека) и искусственно введенный порядковый номер текущего чека, начиная с 1, для каждого покупателя.
```sql
select customer_id, 
row_number() over (partition by customer_id order by order_date),
row_number() over (partition by customer_id) -- честно хз, что тут хотело условие
from sales.sales_order_detail as det
join sales.sales_order_header as head
on det.sales_order_id = head.sales_order_id
```
## Task 4
Вывести номера продуктов, таких что их цена выше средней цены продукта в подкатегории, к которой относится продукт. Запрос реализовать двумя способами. В одном из решений допускается использование обобщенного табличного выражения (WITH).

Without CTE
```sql
select name from (
select name,
	list_price,
	avg(list_price) over (partition by product_subcategory_id) as avg_price
from production.product)
where list_price > avg_price
```
With CTE
```sql
with prices as (
select name,
	list_price,
	avg(list_price) over (partition by product_subcategory_id) as avg_price
from production.product)

select name from prices
where list_price > avg_price
```
## Task 5
Вывести номер продукта, название продукта, а также среднее количество этого продукта по трём последним по дате чекам, в которых был этот продукт. Использовать оконные рамки (ROWS BETWEEN).
```sql
SELECT DISTINCT
    product_id,
	AVG(order_qty) OVER(
		PARTITION BY product_id 
		ORDER BY modified_date DESC
		ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
	)
FROM sales.sales_order_detail;
```
