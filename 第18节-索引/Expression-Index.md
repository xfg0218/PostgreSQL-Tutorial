**摘要**：在本教程中，您将学习如何使用 `PostgreSQL` 表达式索引根据表达式的结果而不是列数据创建索引。

# `PostgreSQL` 表达式索引概述

创建索引时，您可以指定要包含在索引中的表的一个或多个列。`PostgreSQL` 将从这些列中提取值以创建索引。当基于索引列查询数据时，`PostgreSQL` 使用索引进行快速查找。

您可以根据涉及表列而不是列数据的表达式的结果创建表达式索引。创建表达式索引时，`PostgreSQL` 会计算表达式并使用结果进行索引。

当您使用表达式从表中查询数据时，`PostgreSQL` 将利用表达式索引来提高性能。

要创建表达式索引，请使用以下形式的 `CREATE INDEX` 语句：

```sql
CREATE INDEX [index_name]
ON table_name(expression);
```

在此语法中：

- 首先，在 `CREATE INDEX` 关键字后指定索引的名称。索引名称是可选的。
- 其次，提供要在其上创建索引的表的名称。
- 第三，定义用于创建索引的表达式。

# 根据函数的结果创建表达式索引

我们将使用 `inventory` 数据库中的 `products` 表：

![images](../images/expression-index.png)

首先，为 `products` 表的 `product_name` 列创建一个索引：

```sql
CREATE INDEX ON products (product_name);
```

其次，找到 `apple iphone 15` 的产品：

```sql
EXPLAIN
SELECT
  product_name,
  price
FROM
  products
WHERE
  LOWER(product_name) = 'apple iphone 15';
```

此查询不使用索引 `products_product_name_idx` 。

第三，为 `products` 表创建表达式索引：

```sql
CREATE INDEX ON products (LOWER(product_name));
```

此语句创建一个名为 `products_lower_idx` 的新索引。

最后，运行使用 `LOWER()` 函数查找产品的查询：

```sql
EXPLAIN
SELECT
  product_name,
  price
FROM
  products
WHERE
  LOWER(product_name) = 'apple iphone 15';
```

输出：

```sql
                                   QUERY PLAN
---------------------------------------------------------------------------------
 Bitmap Heap Scan on products  (cost=4.32..15.09 rows=5 width=17)
   Recheck Cond: (lower((product_name)::text) = 'apple iphone 15'::text)
   ->  Bitmap Index Scan on products_lower_idx  (cost=0.00..4.31 rows=5 width=0)
         Index Cond: (lower((product_name)::text) = 'apple iphone 15'::text)
(4 rows)
```

输出指示查询使用表达式索引来搜索产品。

# 根据表达式的结果创建表达式索引

首先，根据安全库存的值创建一个表达式索引：

```sql
CREATE INDEX ON products ((price * safety_stock));
```

此语句创建名称为 `products_expr_idx` 的表达式索引。

> 请注意，您必须将表达式放在括号内。因此，表名 `((expression))` 后面有两个括号。

其次，按安全库存值查询产品：

```sql
EXPLAIN
SELECT
  product_name,
  price,
  safety_stock
FROM
  products
WHERE
  price * safety_stock > 1000;
```

输出：

```sql
                                    QUERY PLAN
-----------------------------------------------------------------------------------
 Bitmap Heap Scan on products  (cost=10.93..29.91 rows=342 width=21)
   Recheck Cond: ((price * (safety_stock)::numeric) > '1000'::numeric)
   ->  Bitmap Index Scan on products_expr_idx  (cost=0.00..10.84 rows=342 width=0)
         Index Cond: ((price * (safety_stock)::numeric) > '1000'::numeric)
```

输出指示查询使用表达式索引。

# 何时使用表达式索引

在实践中，您会发现表达式索引在以下情况下很有用：

- 区分大小写的搜索。
- 根据表达式筛选和排序行。
- 根据日期或时间的部分筛选行。
- 使用 `JSONB_EXTRACT_PATH` 函数筛选 `JSON` 数据。
- 使用 `ARRAY_LENGTH` 函数过滤数组数据。

# 总结

- 使用表达式索引来优化涉及表达式的查询。