## Task 1
Показать все названия товаров из подкатегории Mountain Bikes (коллекции prod_products и prod_product_subcategories).
```javascript
db.prod_products.aggregate([
        { $lookup: {from: "prod_product_subcategories",
                    localField: "product_subcategory_id",
                    foreignField: "product_subcategory_id",
                    as: "subcat"}},
        {$unwind: "$subcat"},
        {$match: {"subcat.name" : "Mountain Bikes"}},
        {$project : {"name" : 1}}
])
```
## Task 2
Вывести список заказов, сделанных в 2012 году (коллекция sales_sales_order_headers).
```javascript
db.sales_sales_order_headers.aggregate([
    {$match: {$expr: {$eq: [{$year : "$order_date"}, 2012]}}},
    {$project : {"sales_order_id" : 1}}
])
```
## Task 3
Вывести список названий товаров и их количество (Quantity) в заказах (коллекции sales_sales_order_details и prod_products).
```javascript
db.sales_sales_order_details.aggregate([
    {$lookup: {from: "prod_products",
                localField: "product_id",
                foreignField: "product_id",
                as: "product"}},
    {$group: {_id: "$product.product_id", quantity: {$sum : "$order_qty"}}}
])
```
## Task 4
Для каждого сотрудника вывести должность (job_title), дату приема на работу (hire_date) и сумму всех выплат (employee_pay_history) (коллекция hr_employees).
```javascript
db.hr_employees.find({}, {
    ob_title: 1, 
    hire_date: 1, 
    sum_payments: {
        $sum: "$employee_pay_history.rate"
    }
})
```
## Task 5
Найти общее количество проданных товаров (сумма order_qty) по каждому заказу (sales_order_header_ref). Используйте агрегацию (коллекция sales_sales_order_details)
```javascript
db.sales_sales_order_details.aggregate([
    {$group: {_id: "$sales_order_id", sum_qty: {$sum: "$order_qty"}}},
    {$project: {order_id : "$sales_order_id", sum_qty : "$sum_qty"}}
])
```
## Task 6
Вычислить среднюю стоимость товара (list_price) по каждой подкатегории (product_subcategory_id) (коллекция prod_products).
```javascript
db.prod_products.aggregate([
    {$group: {_id: "$product_subcategory_id", avg_price: {$sum: "$list_price"}}}
])
```
## Task 7
Определить, сколько заказов оформил каждый сотрудник (sales_person_ref) в 2013 году (коллекция sales_sales_order_headers).
```javascript
db.sales_sales_order_headers.aggregate([
    {$match: {$expr: {$eq: [{$year: "$order_date"}, 2013]}}},
    {$group: {_id: "$sales_person_ref", order_count: {$count: {}}}},
])
```
## Task 8
Найти подкатегории товаров, у которых более 10 товаров (коллекция prod_products).
```javascript
db.prod_products.aggregate([
    {$group: {_id: "$product_subcategory_id", count_prod: {$count: {}}}},
    {$match: {$expr: {$gte: ["$count_prod", 10]}}}
])
```
## Task 9
Вывести список заказов, в которых купили более 5 единиц одного товара (коллекция sales_sales_order_details).
```javascript
db.sales_sales_order_details.aggregate([
    {$match: {$expr: {$gt: ["$order_qty", 5]}}},
    {$group: {_id: "$sales_order_id"}}
])
```
## Task 10
Показать клиентов, которые совершили более 3 заказов в 2013 году (коллекция sales_sales_order_headers).
```javascript
db.sales_sales_order_headers.aggregate([
    {$match: {$expr: {$eq: [{$year: "$order_date"}, 2013]}}},
    {$group: {_id: "$customer_id", count: {$count: {}}}},
    {$match: {$expr: {$gt: ["$count", 3]}}}
])
```
## Task 11
Найти сотрудников, у которых день рождения в текущем месяце (коллекция hr_employees).
```javascript
db.hr_employees.find({$expr: {$eq: [{$month: "$birth_date"}, {$month: new Date()}]}})```
```