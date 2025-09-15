**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 子查询，以更简单的方式编写更灵活的查询。

# `PostgreSQL` 子查询入门

`PostgreSQL` 允许你在一个查询中嵌入另一个查询。这种被嵌入的查询称为子查询。包含子查询的查询称为外部查询。

本质上，子查询是嵌入在外部查询中的查询。

第一个重要的问题是子查询应放在查询中的哪个位置。

为了回答这个问题，让我们再看一下 `SELECT` 语句：

```sql
SELECT
  select_list
FROM
  table1
  JOIN table2 ON join_condition;
WHERE
  condition;
```

在这个 `SELECT` 语句中：

- `SELECT` 子句可以接受单个值（一个表达式或一列）。
- `FROM` 和 `JOIN` 子句可以接受由行和列组成的表或结果集。
- `WHERE` 子句可以根据条件的运算符接受单个值或列。

根据每个子句可接受的数据形式，你可以使用合适的子查询。

# `WHERE` 子句中的 `PostgreSQL` 子查询

假设你想在 `products` 表中找到最昂贵的产品。为此，你可能需要执行两个查询

首先，从 `products` 表中选择最高价格：

```sql
SELECT
  MAX(price)
FROM
  products;
```

该查询返回一个单一值：

```sql
   max
---------
 2999.99
```

其次，查询价格等于最高价格的产品：

```sql
SELECT
  product_name,
  price
FROM
  products
WHERE
  price = 2999.99;
```

输出：

```sql
      product_name       |  price
-------------------------+---------
 Samsung QN900C Neo QLED | 2999.99
```

在第二个查询中，`WHERE` 子句中的条件可以接受返回单个值的查询。因此，你可以像这样将返回单个值的第一个查询用作第二个查询中的子查询：

```sql
SELECT
  product_name,
  price
FROM
  products
WHERE
  price = (
    SELECT
      MAX(price)
    FROM
      products
  );
```

输出：

```sql
      product_name       |  price
-------------------------+---------
 Samsung QN900C Neo QLED | 2999.99
```

同样地，你可以找到价格高于平均价格的产品：

```sql
SELECT
  product_name,
  price
FROM
  products
WHERE
  price > (
    SELECT
      AVG(price)
    FROM
      products
  );
```

输出：

```sql
        product_name        |  price
----------------------------+---------
 Samsung Galaxy Z Fold 5    | 1799.99
 Apple iPhone 15 Pro Max    | 1299.99
 LG OLED TV C3              | 1999.99
 Sony Bravia XR A95K        | 2499.99
 Samsung QN900C Neo QLED    | 2999.99
 LG G3 OLED                 | 2499.99
 Sony HT-A7000 Soundbar     | 1299.99
 Dell XPS 15                | 1499.99
 HP Spectre x360            | 1399.99
 Microsoft Surface Laptop 5 | 1299.99
 Lenovo ThinkPad X1 Carbon  | 1599.99
 Apple iMac 24"             | 1299.99
```

# `SELECT` 子句中的 `PostgreSQL` 子查询

`SELECT` 子句接受一个值。你可以在 `SELECT` 子句中使用返回单个值的子查询。

例如，以下查询使用子查询来检索品牌ID为2的产品的产品名称、价格和最高价格：

```sql
SELECT
  product_name,
  price,
  (
    SELECT
      MAX(price)
    FROM
      products
    WHERE
      brand_id = 2
  ) max_price
FROM
  products
WHERE
  brand_id = 2;
```

输出：

```sql
      product_name       |  price  | max_price
-------------------------+---------+-----------
 Apple iPhone 15         | 1099.99 |   1299.99
 Apple iPhone 15 Pro Max | 1299.99 |   1299.99
 Apple iPad Pro 12.9     | 1099.99 |   1299.99
 Apple AirPods Pro 3     |  249.99 |   1299.99
 Apple Watch Series 9    |  399.99 |   1299.99
 Apple iMac 24"          | 1299.99 |   1299.99
```

以下示例使用子查询返回产品名称及其库存数量：

```sql
SELECT
  p.product_name,
  (
    SELECT
      SUM(quantity)
    FROM
      inventories
    WHERE
      product_id = p.product_id
  ) AS inventory_qty
FROM
  products p
ORDER BY
  product_name;
```

部分输出：

```sql
        product_name        | inventory_qty
----------------------------+---------------
 Apple AirPods Pro 3        |           180
 Apple iMac 24"             |           320
 Apple iPad Pro 12.9        |           170
 Apple iPhone 15            |           150
 Apple iPhone 15 Pro Max    |           140
...
```

# `FROM` 子句中的 `PostgreSQL` 子查询

你可以在 `FROM` 子句中使用返回结果集（行和列）的子查询。

例如，以下语句在 `FROM` 子句中使用子查询来返回所有品牌的平均产品数量：

```sql
SELECT
  AVG(product_count)
FROM
  (
    SELECT
      brand_id,
      COUNT(*) product_count
    FROM
      products
    GROUP BY
      brand_id
  );
```

输出：

```sql
        avg
--------------------
 2.5000000000000000
(1 row)
```

子查询使用带有 `COUNT` 函数的 `GROUP BY` 子句返回包含 `brand_id` 和产品数量的结果集：

```sql
SELECT
  brand_id,
  count(*) product_count
FROM
  products
GROUP BY
  brand_id
```

输出：

```sql
 brand_id | product_count
----------+---------------
        9 |             1
        5 |             3
        4 |             1
       10 |             1
        6 |             2
        2 |             5
        7 |             3
        1 |             5
        8 |             1
```

外部查询使用 `AVG()` 函数计算所有品牌的平均产品数量。

# `JOIN` 子句中的 `PostgreSQL` 子查询

`JOIN` 子句接受一个结果集，即行和列的集合。

在 `JOIN` 子句中使用子查询时，子查询必须返回一个结果集。此外，它还必须包含用于与外部查询中的表进行连接的列。

以下语句在 `JOIN` 子句中使用子查询来检索库存数量大于300的产品：

```sql
SELECT
  product_name,
  quantity
FROM
  products
  JOIN (
    SELECT
      product_id,
      quantity
    FROM
      inventories
    WHERE
      quantity > 300
  ) USING (product_id);
```

输出：

```sql
       product_name        | quantity
---------------------------+----------
 Lenovo ThinkPad X1 Carbon |      310
 Apple iMac 24"            |      320
 Dell Inspiron 27          |      330
```

首先，子查询从 `inventories` 表中检索 `product_id` 和数量，其中数量大于300：

```sql
SELECT
  product_id,
  quantity
FROM
  inventories
WHERE
  quantity > 300
```

输出：

```sql
 product_id | quantity
------------+----------
         23 |      310
         24 |      320
         25 |      330
```

其次，外部查询根据 `product_id` 列中的值，将 `products` 表与上述结果集进行连接。

# 总结

- 子查询是嵌套在另一个查询中的查询。
- 包含子查询的查询称为外部查询。
- 在 `SELECT` 语句的 `SELECT` 、 `FROM` 、`JOIN` 和 `WHERE` 子句中使用子查询。

