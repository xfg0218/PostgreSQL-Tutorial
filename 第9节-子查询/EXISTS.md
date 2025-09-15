**摘要** ：在本教程中，你将学习如何使用 `PostgreSQL` 的 `EXISTS` 运算符来检查子查询返回的行是否存在。

# `PostgreSQL EXISTS` 运算符入门

在 `PostgreSQL` 中，`EXISTS` 运算符允许您检查子查询是否返回至少一行。如果子查询返回任何行，`EXISTS` 运算符将返回 `true` ，否则返回 `false` 。

以下是 `EXISTS` 运算符的语法：

```sql
EXISTS (subquery)
```

要否定 `EXISTS` 运算符的结果，可以使用 `NOT` 运算符：

```sql
NOT EXISTS (subquery)
```

通常，您会在 `SELECT` 、`UPDATE` 和 `DELETE` 语句的 `WHERE` 子句中使用`EXISTS` 运算符。

# 在 `SELECT` 语句中使用 `PostgreSQL` 的 `EXISTS` 运算符

以下示例使用 `EXISTS` 运算符查找至少存储在一个仓库中的所有产品：

```sql
SELECT
  product_name
FROM
  products p
WHERE
  EXISTS (
    SELECT
      1
    FROM
      inventories i
    WHERE
      i.product_id = p.product_id
  );
```

输出：

```sql
       product_name
---------------------------
 Sony Xperia 1 VI
 Samsung Galaxy Z Fold 5
 Samsung Galaxy Tab S9
 Apple iPad Pro 12.9
 Samsung Galaxy Buds Pro 2
 Apple Watch Series 9
 LG OLED TV C3
 Sony Bravia XR A95K
 LG G3 OLED
 Sony HT-A7000 Soundbar
 Dell XPS 15
 HP Spectre x360
 Lenovo ThinkPad X1 Carbon
 Apple iMac 24"
```

在这个示例中，子查询会检查 `inventories` 表中是否存在 `product_id` 等于`products` 表中 `product_id` 的行： 

```sql
SELECT
  1
FROM
  inventories i
WHERE
  i.product_id = p.product_id
```

如果子查询返回任何行，`EXISTS` 运算符会返回 `true` ，且外部查询会将该产品包含在结果集中。

# 在 `UPDATE` 语句中使用 `PostgreSQL` 的 `EXISTS` 运算符

以下语句将仓库 `ID` 为 `1` 中所有产品的价格提高 `5%` ：

```sql
UPDATE products p
SET
  price = price * 1.05
WHERE
  EXISTS (
    SELECT
      1
    FROM
      inventories i
    WHERE
      i.product_id = p.product_id
      AND i.warehouse_id = 1
  ) 
RETURNING *;
```

输出：

```sql
 product_id |        product_name        |  price  | brand_id | category_id
------------+----------------------------+---------+----------+-------------
          1 | Samsung Galaxy S24         | 1049.99 |        1 |           1
          4 | Xiaomi Mi 14               |  839.99 |        4 |           2
          7 | Apple iPhone 15 Pro Max    | 1364.99 |        2 |           3
         10 | Apple AirPods Pro 3        |  262.49 |        2 |           5
         13 | Samsung Galaxy Watch 6     |  367.49 |        1 |           6
         16 | Samsung QN900C Neo QLED    | 3149.99 |        1 |           8
         19 | Bose SoundLink Max         |  419.99 |        7 |           9
         22 | Microsoft Surface Laptop 5 | 1364.99 |        9 |          11
         25 | Dell Inspiron 27           | 1049.99 |        7 |          12
```

在这个示例中，子查询会检查 `inventory` 表中是否存在任何行，其 `product _id` 与 `product_id` 表中的 `product_id` 相同，且 `warehouse_id` 为 `1` 。

如果子查询产生至少一行数据，`EXISTS` 运算符返回 `true` ，且 `UPDATE` 语句会更新产品价格。

你会发现在带有 `EXISTS` 运算符的子查询中使用 `SELECT 1` 是一种常见做法。

数字 `1` 是一个占位符，不会影响查询逻辑。此外，`SELECT 1` 的效率很高，因为它避免了从表中选择数据的开销。

# 在 `DELETE` 语句中使用 `PostgreSQL` 的 `EXISTS` 运算符

首先，向 `products` 表中插入两行新数据：

```sql
INSERT INTO
  products (product_name, price, safety_stock, gross_weight, brand_id, category_id)
VALUES
  ('Samsung Galaxy S25', 999.99, 10, 0.36, 1, 1),
  ('Apple iPhone 17', 1299.99, 20, 0.41, 2, 1);
```

其次，删除未存储在任何仓库中的产品：

```sql
DELETE FROM products p
WHERE
  NOT EXISTS (
    SELECT
      1
    FROM
      inventories i
    WHERE
      i.product_id = p.product_id
  ) 
RETURNING *;
```

输出：

```sql
 product_id |    product_name    |  price  | brand_id | category_id
------------+--------------------+---------+----------+-------------
         26 | Samsung Galaxy S25 |  999.99 |        1 |           1
         27 | Apple iPhone 17    | 1299.99 |        2 |           1
```

在这个示例中，子查询会检查 `inventories` 表中是否存在与产品表中相同的样本`product_id` 的行。

如果子查询未返回任何行，则 `NOT EXISTS` 为真，这会导致 `DELETE` 语句删除该产品。

# 总结

- `PostgreSQL` 的 `EXISTS` 运算符在子查询产生任何行时返回 `true`，否则返回 `false`。
- 使用 `EXISTS` 运算符基于相关表中的数据进行检查。