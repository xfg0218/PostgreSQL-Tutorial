**摘要** ：在本教程中，您将学习如何使用 `PostgreSQL` 物化视图来物理存储查询结果。

# `PostgreSQL` 物化视图入门

常规视图不存储数据。当您从它们中查询数据时，`PostgreSQL` 会从底层表中检索数据。

当视图具有复杂的定义查询时，如果非常频繁地从视图中查询数据，则会遇到性能问题。

为了解决这个问题，`PostgreSQL` 引入了物理存储查询结果的物化视图。这些物化视图可以显著提高具有复杂查询的视图的性能。

# 创建 `PostgreSQL` 物化视图

在 `PostgreSQL` 中，您可以使用 `CREATE MATERIALIZED VIEW` 语句创建物化视图：

```sql
CREATE MATERIALIZED VIEW [IF NOT EXISTS] view_name
AS
query
WITH DATA;
```

在此语法中：

- 首先，在 `CREATE MATERIALIZED VIEW` 子句中定义视图名称。如果视图不存在，则 `IF NOT EXISTS` 选项有条件地创建视图。它可以防止错误创建已存在的视图。
- 其次，提供一个查询，在 `AS` 关键字后定义视图。
- 第三，在发出 `CREATE MATERIALIZED VIEW` 时，使用 `WITH DATA` 将数据从基础表加载到视图中。

以下示例使用 `CREATE MATERIALIZED VIEW` 创建汇总 `products` 和 `inventories` 表中数据的具体化视图：

```sql
CREATE MATERIALIZED VIEW inventory_view 
AS
SELECT
  p.product_name,
  SUM(i.quantity) AS quantity
FROM
  inventories i
  JOIN products p ON i.product_id = p.product_id
GROUP BY
  p.product_name
WITH DATA;
```

# 从物化视图查询数据

由于您在 `CREATE MATERIALIZED VIEW` 语句中使用了 `WITH DATA` ，因此 `PostgreSQL` 在执行语句时将数据加载到视图中。

以下示例使用 `SELECT` 语句从 `inventory_view` 中检索数据：

```sql
SELECT * FROM inventory_view
ORDER BY quantity DESC;
```

输出：

```sql
        product_name        | quantity
----------------------------+----------
 Dell Inspiron 27           |      330
 Apple iMac 24"             |      320
 Lenovo ThinkPad X1 Carbon  |      310
...
```

# 刷新数据

具体化视图不会自动更新基础表中的数据。

要将具体化视图的数据替换为表中的新数据，请使用 `REFRESH MATERIALIZED VIEW` 语句：

```sql
REFRESH MATERIALIZED VIEW view_name;
```

例如：

首先，从 `inventory_view` 中获取总数量：

```sql
SELECT
  SUM(quantity)
FROM
  inventory_view;
```

输出：

```sql
 sum
------
 5360
```

其次，将产品 `ID 1` 的数量增加到 `200` ：

```sql
UPDATE inventories
SET
  quantity = 200
WHERE
  product_id = 1 
RETURNING *;
```

输出：

```sql
 inventory_id | product_id | warehouse_id | quantity
--------------+------------+--------------+----------
            1 |          1 |            1 |      200
```

第三，将数据刷新到物化视图 `inventory_view` ：

```sql
REFRESH MATERIALIZED VIEW inventory_view;
```

最后，通过查询 `inventory_view` 中的数据来验证更新：

```sql
SELECT SUM(quantity)
FROM inventory_view;
```

输出：

```sql
 sum
------
 5460
```

# 删除物化视图 

要删除物化视图，请使用 `DROP MATERIALIZED VIEW` 语句：

```sql
DROP MATERIALIZED VIEW [ IF EXISTS ] view_name
CASCADE;
```

在此语法中：

- 首先，在 `DROP MATERIALIZED VIEW` 子句中指定一个或多个具体化视图名称。`IF EXISTS` 可防止错误删除不存在的具体化视图。
- 其次，使用 `CASCADE` 选项删除具有依赖对象的物化视图。`CASCADE` 选项还会删除视图以及从属对象。

例如，以下内容使用 `DROP MATERIALIZED VIEW` 语句删除 `inventory_view`：

```sql
DROP MATERIALIZED VIEW inventory_view CASCADE;
```

# 为什么使用物化视图

物化视图可以通过存储复杂查询的预计算结果集来提高查询速度。

在实践中，物化视图有助于聚合来自多个表的数据以进行报告。

# 总结

- 物化视图以物理方式存储数据。
- 使用 `CREATE MATERIALIZED VIEW` 语句创建具体化视图。
- 使用 `REFRESH MATERIALIZED VIEW` 语句将物化视图中的数据替换为新数据。
- 使用 `DROP MATERIALIZED VIEW` 语句删除具体化视图。