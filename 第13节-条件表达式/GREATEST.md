**摘要**：在本教程中，您将学习如何使用 `PostgreSQL GREATEST` 函数从值列表中选择最高值。

# `PostgreSQL GREATEST` 函数入门

`GREATEST` 函数接受值列表并返回最大值。

这是 `GREATEST` 函数的语法：

```sql
GREATEST (expression1, expression2, ....)
```

`GREATEST` 函数忽略 `NULL` 如果所有表达式的计算结果都为 `NULL` 它将返回 `NULL` 。

> 在 `SQL` 标准中，如果任何表达式的计算结果为 `NULL` 则 `GREATEST` 函数返回 `NULL`。有些数据库的行为方式类似于 `MySQL` 。

# 基本 `PostgreSQL GREATEST` 函数示例

以下示例使用 `GREATEST` 函数返回列表中的最大数字：

```sql
SELECT GREATEST(1,2,3) highest;
```

输出：

```sql
 highest
---------
       3
```

`GREATEST` 函数返回 `3` ，因为它是列表中的最高数字。

以下示例使用 `GREATEST` 函数按字母顺序返回最大字符串：

```sql
SELECT GREATEST('A','B','C') largest;
```

输出：

```sql
 largest
---------
 C
```

`GREATEST` 函数返回字母 `C` 是按字母顺序排列的 `A` `B` 和 `C` 中的最大值。

以下语句使用 `GREATEST` 函数来比较两个布尔值：

```sql
SELECT GREATEST(true, false) largest;
```

输出：

```sql
 largest
---------
 t
```

比较 `true` 和 `false` 时，`true` 大于 `false` ，因此 `GREATEST` 函数返回 `true` 。

以下示例使用 `GREATEST` 函数在日期文字列表中查找最新日期：

```sql
SELECT
  GREATEST ('2025-01-01', '2025-02-01', '2025-03-01', NULL) latest;
```

输出：

```sql
   latest
------------
 2025-03-01
```

在此示例中，`GREATEST` 函数返回 `March 1` , `2025` ，因为它是 `January 1` , `2025` 、`February 1 2025` 和 `March 1` , `2025` 之间的最新日期。

请注意，在此示例中，`GREATEST` 函数忽略 `NULL` 。

# 将 `PostgreSQL GREATEST` 函数与表数据一起使用

我们将使用 `inventory` 数据库中 `products` 

假设运费计算如下：

- 每磅 5 美元。
- 最低运费为 10 美元。

以下语句使用 `GREATEST` 函数来确保至少最低费用：

```sql
SELECT
  product_name,
  gross_weight,
  GREATEST (gross_weight * 5, 10) AS shipping_cost
FROM
  products
ORDER BY
  product_name
```

输出：

```sql
        product_name        | gross_weight | shipping_cost
----------------------------+--------------+---------------
 Apple AirPods Pro 3        |         0.01 |            10
 Apple iMac 24"             |         9.92 |         49.60
 Apple iPad Pro 12.9        |         1.42 |            10
 Apple iPhone 15            |         0.38 |            10
 Apple iPhone 15 Pro Max    |         0.49 |            10
 Apple Watch Series 9       |         0.09 |            10
 Bose SoundLink Max         |         6.61 |         33.05
 Dell Inspiron 27           |        17.64 |         88.20
...
```

该报表使用产品的毛重和运费（5 美元/磅）计算运费。如果运费小于 10，则最大函数返回 10 以确保最小成本。

# 总结

- 使用 `GREATEST` 函数返回值列表中的最大值。
- `GREATEST` 函数忽略 `NULL`

