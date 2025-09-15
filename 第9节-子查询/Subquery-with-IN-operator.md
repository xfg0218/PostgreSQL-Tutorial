**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `WHERE IN subquery` ，根据子查询返回的一系列值来筛选行。

# `PostgreSQL WHERE IN` 子查询入门

在 `PostgreSQL` 中，`WHERE IN subquery` 允许你检查某个值是否在子查询返回的一组值中。

以下是在 `SELECT` 语句中使用 `WHERE IN subquery` 的语法：

```sql
SELECT select_list
FROM table_name
WHERE column_name IN (subquery);
```

在以下语法中：

- 首先，在带有 `IN` 运算符的 `WHERE` 子句中指定列名以筛选行。 
- 其次，将子查询放在括号 `()` 内，为 `IN` 运算符提供值。

该查询返回一个结果集，其中 `column_name` 中的值与子查询返回的任何值匹配。

如果子查询没有返回任何行，那么外部查询也会返回一个空的结果集。

# `PostgreSQL WHERE IN` 子查询示例

以下示例使用 `WHERE IN subquery` 查找存储在 `ID` 为 `1` 的仓库中的所有产品：

```sql
SELECT
  product_name
FROM
  products
WHERE
  product_id IN (
    SELECT
      product_id
    FROM
      inventories
    WHERE
      warehouse_id = 1
  );
```

输出：

```sql
        product_name
----------------------------
 Xiaomi Mi 14
 Apple iPhone 15 Pro Max
 Apple AirPods Pro 3
 Samsung Galaxy Watch 6
 Samsung QN900C Neo QLED
 Bose SoundLink Max
 Microsoft Surface Laptop 5
 Dell Inspiron 27
```

在这个示例中：

- 首先，子查询检索存储在仓库 `ID 1` 中的 `product_id` 列表。
- 外部查询选择那些 `product_id` 与子查询返回的任何 `product_id` 相匹配的产品。

# `PostgreSQL WHERE NOT IN` 子查询示例

你可以使用 `NOT IN` 运算符来筛选不在查询返回的列表中的行。

以下语句使用 `WHERE NOT IN subquery` 来查找所有未存储在仓库 `ID` 为 `1` 的产品：

```sql
SELECT
  product_name
FROM
  products
WHERE
  product_id NOT IN (
    SELECT
      product_id
    FROM
      inventories
    WHERE
      warehouse_id = 1
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

# 使用 `PostgreSQL` 的 `WHERE NOT IN` 与 `UPDATE` 语句结合

以下语句展示了如何在 `UPDATE` 语句中使用 `WHERE IN subquery` 来筛选要更新的行：

```sql
UPDATE table_name
SET column2 = value2,
    column3 = value3,
    ...
WHERE column1 IN (subquery);
```

`UPDATE` 语句会更新那些在 `column1` 中的值与子查询返回的任何值相匹配的行。

如果没有匹配项或者子查询未返回任何行，`UPDATE` 语句将不会更新任何行。

以下示例将仓库 `ID` 为 `1` 中存储的产品价格降低 `10%`：

```sql
UPDATE products
SET
  price = price * 0.9
WHERE
  product_id IN (
    SELECT
      product_id
    FROM
      inventories
    WHERE
      warehouse_id = 1
  ) RETURNING *;
```

输出：

```sql
 product_id |        product_name        |  price  | brand_id | category_id
------------+----------------------------+---------+----------+-------------
          4 | Xiaomi Mi 14               |  719.99 |        4 |           2
          7 | Apple iPhone 15 Pro Max    | 1169.99 |        2 |           3
         10 | Apple AirPods Pro 3        |  224.99 |        2 |           5
         13 | Samsung Galaxy Watch 6     |  314.99 |        1 |           6
         16 | Samsung QN900C Neo QLED    | 2699.99 |        1 |           8
         19 | Bose SoundLink Max         |  359.99 |        7 |           9
         22 | Microsoft Surface Laptop 5 | 1169.99 |        9 |          11
         25 | Dell Inspiron 27           |  899.99 |        7 |          12
```

工作原理：

- 子查询检索存储在仓库 `ID 1` 中的 `product_id` 列表。
- 然后，`UPDATE` 语句将所选产品的价格降低 `10%` 。

# 将 `WHERE IN` 子查询与 `DELETE` 语句结合使用

以下展示了如何在 `DELETE` 语句中使用 `WHERE IN subquery` 来选择要删除的行：

```sql
DELETE FROM table_name
WHERE column1 IN (subquery);
```

这条 `DELETE` 语句将删除 `column1` 中值与子查询返回的任何值相匹配的行。

如果子查询未返回任何行或没有匹配项，`DELETE` 语句将不会删除任何行。

以下示例展示了如何删除存储在仓库 `ID` 为 `1` 中的产品：

```sql
DELETE FROM products
WHERE
  product_id IN (
    SELECT
      product_id
    FROM
      inventories
    WHERE
      warehouse_id = 1
  ) 
RETURNING *;
```

输出：

```sql
 product_id |        product_name        |  price  | brand_id | category_id
------------+----------------------------+---------+----------+-------------
          4 | Xiaomi Mi 14               |  719.99 |        4 |           2
          7 | Apple iPhone 15 Pro Max    | 1169.99 |        2 |           3
         10 | Apple AirPods Pro 3        |  224.99 |        2 |           5
         13 | Samsung Galaxy Watch 6     |  314.99 |        1 |           6
         16 | Samsung QN900C Neo QLED    | 2699.99 |        1 |           8
         19 | Bose SoundLink Max         |  359.99 |        7 |           9
         22 | Microsoft Surface Laptop 5 | 1169.99 |        9 |          11
         25 | Dell Inspiron 27           |  899.99 |        7 |          12
```

工作原理。

- 子查询检索存储在仓库 `1` 中的所有 `product_id`。
- `DELETE` 语句会从 `products` 表中删除所有具有这些 `product_id` 的产品。

# 总结

- 使用 `WHERE IN subquery` 可根据动态值集筛选行。