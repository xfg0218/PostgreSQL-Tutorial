**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `CASE` 表达式在查询中执行条件逻辑。

# `PostgreSQL CASE` 表达式入门

在 `PostgreSQL` 中，`CASE` 表达式是一个强大的工具，允许你在查询中执行条件逻辑。

> `CASE` 表达式的作用类似于其他编程语言中的 `if-else` 语句。

以下是 `CASE` 表达式的语法：

```sql
CASE  
   WHEN condition1 THEN result1
   WHEN condition2 THEN result2
   WHEN condition3 THEN result3
   ...
   ELSE result
END
```

在以下语法中：

每个条件（ `condition1` 、 `condition2` 、 `condition3` .... ）都是一个布尔表达式，其返回值为 `true` 或 `false` 。`CASE` 表达式会从上到下依次对这些条件进行求值。

如果条件为真，`CASE` 表达式会返回 `THEN` 后面相应的结果，并停止评估后续条件。

例如，如果 `condition1` 为真，`CASE` 表达式会返回 `result1` ，且不会对 `condition2` 和 `condition3` 进行求值。

如果没有条件为 `true` ，则 `CASE` 表达式返回 `ELSE` 子句的 `result` 。`ELSE` 子句是可选的。如果省略该子句且没有条件返回真，那么 `CASE` 表达式将返回 `NULL` 。

你可以在使用表达式的查询中使用 `CASE` 表达式。例如，你可以在 `SELECT` 语句的 `SELECT` 和 `WHERE` 子句中使用 `CASE` 表达式。

# `PostgreSQL CASE` 表达式示例

以下语句返回产品名称及其在库存中的总数量：

```sql
SELECT
  product_name,
  SUM(quantity) total_quantity
FROM
  inventories
  JOIN products USING (product_id)
GROUP BY
  product_name
ORDER BY
  total_quantity;
```

输出：

```sql
        product_name        | total_quantity
----------------------------+----------------
 Samsung Galaxy Z Fold 5    |            110
 Xiaomi Mi 14               |            120
 Sony Xperia 1 VI           |            130
 Apple iPhone 15 Pro Max    |            140
 Apple iPhone 15            |            150
 Samsung Galaxy Tab S9      |            160
 Apple iPad Pro 12.9        |            170
 Apple AirPods Pro 3        |            180
 Samsung Galaxy Buds Pro 2  |            190
...
```

要为每个产品分配库存水平，您可以使用 `CASE` 表达式。

```sql
SELECT
  product_name,
  SUM(quantity) total_quantity,
  CASE
    WHEN SUM(quantity) <= 120 THEN 'Low'
    WHEN SUM(quantity) > 120  AND SUM(quantity) <= 150 THEN 'Medium'
    WHEN SUM(quantity) > 150 THEN 'High'
  END inventory_level
FROM
  inventories
  JOIN products USING (product_id)
GROUP BY
  product_name
ORDER BY
  total_quantity;
```

输出：

```sql
        product_name        | total_quantity | inventory_level
----------------------------+----------------+-----------------
 Samsung Galaxy Z Fold 5    |            110 | Low
 Xiaomi Mi 14               |            120 | Low
 Sony Xperia 1 VI           |            130 | Medium
 Apple iPhone 15 Pro Max    |            140 | Medium
 Apple iPhone 15            |            150 | Medium
 Samsung Galaxy Tab S9      |            160 | High
 Apple iPad Pro 12.9        |            170 | High
 Apple AirPods Pro 3        |            180 | High
 Samsung Galaxy Buds Pro 2  |            190 | High
...
```

在本语句中，我们会根据每种产品的总数量为其分配一个库存水平。

如果总数量小于或等于120，其库存水平为低。

如果总数量大于120且小于或等于150，其库存水平为中等。

如果总数量超过150，则库存偏高。

# 简单的 `PostgreSQL CASE` 表达式

如果您有一个条件需要评估，那么可以使用另一种 `CASE` 表达式：

```sql
CASE expression
  WHEN value1 THEN result1
  WHEN value2 THEN result2
  WHEN value3 THEN result3
  ...
  ELSE result
END
```

在该语法中，`CASE` 会对表达式进行计算，将其结果与 `value1` 、`value2` 、`value3` .... 进行比较，并返回相应的结果。

如果表达式的结果与任何值都不匹配，那么 `CASE` 表达式将返回 `ELSE` 子句中的 `result` 。

`ELSE` 子句是可选的。如果省略 `ELSE` 子句，且表达式与任何值（`value1` 、`value2` 、`value3` 等）都不匹配，那么 `CASE` 表达式将返回`NULL` 。

以下示例使用简单的 `CASE` 表达式使库存交易更加清晰：

```sql
SELECT
  transaction_id,
  type,
  CASE type
    WHEN 'issue' THEN 'Goods Issue'
    WHEN 'receipt' THEN 'Goods Receipt'
  END transaction_type
FROM
  transactions;
```

输出：

```sql
 transaction_id |  type   | transaction_type
----------------+---------+------------------
              1 | issue   | Goods Issue
              2 | issue   | Goods Issue
              3 | issue   | Goods Issue
              4 | receipt | Goods Receipt
              5 | receipt | Goods Receipt
...
```

在这个示例中，`CASE` 表达式会计算 `transactions` 表中 `type` 列的值。如果该值为 `issue` ，则返回 `Goods Issue` ；如果 `value` 为`receipt` ，则返回 `Goods Receipt` 。

# 总结

- 使用 `CASE` 表达式向查询添加条件逻辑。