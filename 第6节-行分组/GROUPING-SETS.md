**摘要**：在本教程中，你将学习如何使用PostgreSQL的GROUPING SETS在同一个查询中创建多个分组。

# `PostgreSQL GROUPING SETS` 概述

在 `PostgreSQL` 中，`GROUPING SETS` 是 `GROUP BY` 子句的一项高级功能，它允许你在同一个查询中创建多个分组。

`GROUP BY` 子句允许你创建单个分组。要创建多个分组，你可以使用 `GROUP BY` 子句创建多个查询。

或者，您可以使用 `UNION` 运算符将多个查询与 `GROUP BY` 子句组合起来。

`GROUPING SETS` 支持在同一查询中进行多个分组，而无需使用多个查询或 `UNION` 运算符。

以下是 `GROUPING SETS` 的通用语法：

```sql
SELECT
  column1,
  column2,
  aggregate_function (column3)
FROM
  table_name
GROUP BY
  GROUPING SETS (
      (column1, column2), 
      (column1), 
      (column2), 
      ()
   );
```

在该语法中：

- 首先，在 `GROUP BY` 子句中使用 `GROUPING SETS`。
- 首先，在 `GROUP BY` 子句中使用 `GROUPING SETS`。

该查询创建了四个分组集：

- `(column1, column2)` 定义了一个分组，该分组按 `column1` 和 `column2` 对行进行分组。
- `(column1)` 定义了一个分组，该分组按 `column1` 对行进行分组。
- `(column2)` 定义了一个分组，该分组按 `column2` 对行进行分组。
- `()` 定义了一个分组，表示对所有行的聚合。

请注意，您可以拥有更多或更少的分组集。例如，以下查询将返回三个分组集：

```sql
SELECT
  column1,
  column2,
  aggregate_function (column3)
FROM
  table_name
GROUP BY
  GROUPING SETS (
      (column1), 
      (column2), 
      ()
  );
```

# `PostgreSQL GROUPING SETS` 示例

我们将使用以下 `inventory_reports`  来演示 `GROUPING SETS` 的工作原理。

以下是生成 `inventory_reports` 表的脚本：

```sql
CREATE TABLE inventory_reports(
     warehouse VARCHAR NOT NULL,
     brand VARCHAR NOT NULL,
     quantity INT NOT NULL
);

INSERT INTO inventory_reports(warehouse, brand, quantity)
VALUES
('San Jose', 'Apple', 100),
('San Francisco', 'Apple', 200),
('Texas', 'Apple', 300),
('San Jose', 'Samsung', 50),
('San Francisco', 'Samsung', 100),
('Texas', 'Samsung', 150)
RETURNING *;
```

输出：

```sql
   warehouse   |  brand  | quantity
---------------+---------+----------
 San Jose      | Apple   |      100
 San Francisco | Apple   |      200
 Texas         | Apple   |      300
 San Jose      | Samsung |       50
 San Francisco | Samsung |      100
 Texas         | Samsung |      150
```

以下查询使用 `GROUPING SETS` 返回多个分组：

```sql
SELECT
  warehouse,
  brand,
  SUM(quantity) total
FROM
  inventory_reports
GROUP BY
  GROUPING SETS ((warehouse, brand), (warehouse), (brand), ())
ORDER BY
  warehouse NULLS LAST,
  brand NULLS LAST;
```

输出：

```sql
   warehouse   |  brand  | total
---------------+---------+-------
 San Francisco | Apple   |   200
 San Francisco | Samsung |   100
 San Francisco | NULL    |   300
 San Jose      | Apple   |   100
 San Jose      | Samsung |    50
 San Jose      | NULL    |   150
 Texas         | Apple   |   300
 Texas         | Samsung |   150
 Texas         | NULL    |   450
 NULL          | Apple   |   600
 NULL          | Samsung |   300
 NULL          | NULL    |   900
```

在这个示例中，`GROUPING SETS` 会创建四个分组：

分组 `(warehouse, brand)` 按仓库和品牌对库存进行分组：

```sql
   warehouse   |  brand  | total
---------------+---------+-------
 San Francisco | Apple   |   200
 San Francisco | Samsung |   100
 San Jose      | Apple   |   100
 San Jose      | Samsung |    50
 Texas         | Apple   |   300
 Texas         | Samsung |   150
```

分组 `(warehouse)` 按仓库对库存进行分组：

```sql
   warehouse   |  brand  | total
---------------+---------+-------
 San Francisco | NULL    |   300
 San Jose      | NULL    |   150
 Texas         | NULL    |   450
```

分组 `(brand)` 按品牌对库存进行分组：

```sql
   warehouse   |  brand  | total
---------------+---------+-------
 NULL          | Apple   |   600
 NULL          | Samsung |   300
```

分组 `()` 表示所有仓库和品牌的总库存：

```sql
   warehouse   |  brand  | total
---------------+---------+-------
 NULL          | NULL    |   900
```

# 选择分组

以下示例使用 `GROUPING SETS` 创建两个分组：

```sql
SELECT
  warehouse,
  brand,
  SUM(quantity)
FROM
  inventory_reports
GROUP BY
  GROUPING SETS ((warehouse, brand), ())
ORDER BY
  warehouse NULLS LAST,
  brand NULLS LAST;
```

输出：

```sql
   warehouse   |  brand  | sum
---------------+---------+-----
 San Francisco | Apple   | 200
 San Francisco | Samsung | 100
 San Jose      | Apple   | 100
 San Jose      | Samsung |  50
 Texas         | Apple   | 300
 Texas         | Samsung | 150
 NULL          | NULL    | 900
```

在这个示例中，`GROUPING SETS` 创建了两个分组：

- 分组 `(warehouse, brand)` 按仓库和品牌对库存进行分组。
- 分组 `()` 表示所有仓库和品牌的总库存。

# `PostgreSQL GROUPING` 函数

`GROUPING` 函数接收一个列，如果该列已被聚合，则返回1，否则返回0。

```sql
GROUPING(column_name)
```

例如：

```sql
SELECT
  warehouse,
  brand,
  GROUPING(warehouse) warehouse_grouping,
  GROUPING(brand) brand_grouping,
  SUM(quantity) total
FROM
  inventory_reports
GROUP BY
  GROUPING SETS ((warehouse), (brand), ())
ORDER BY
  warehouse NULLS LAST,
  brand NULLS LAST;
```

输出：

```sql
   warehouse   |  brand  | warehouse_grouping | brand_grouping | total
---------------+---------+--------------------+----------------+-------
 San Francisco | NULL    |                  0 |              1 |   300
 San Jose      | NULL    |                  0 |              1 |   150
 Texas         | NULL    |                  0 |              1 |   450
 NULL          | Apple   |                  1 |              0 |   600
 NULL          | Samsung |                  1 |              0 |   300
 NULL          | NULL    |                  1 |              1 |   900
```

在输出中：

- 带有 `brand_grouping 1` 的行显示了所有品牌按仓库划分的总库存。
- 带有 `warehouse_grouping 1` 的行按品牌显示所有仓库的总库存。
- 带有 `warehouse_grouping` 和 `brand_grouping 1` 的行显示了所有品牌和仓库的总库存。

你可以在 `HAVING` 子句中使用 `GROUPING` 函数来选择按某一列聚合的行。

例如，以下查询使用 `GROUPING` 函数返回按品牌聚合的行：

```sql
SELECT
  warehouse,
  brand,
  GROUPING(warehouse) warehouse_grouping,
  GROUPING(brand) brand_grouping,
  SUM(quantity) total
FROM
  inventory_reports
GROUP BY
  GROUPING SETS ((warehouse), (brand), ())
HAVING
  GROUPING(brand) = 1
ORDER BY
  warehouse NULLS LAST,
  brand NULLS LAST;
```

输出：

```sql
   warehouse   | brand | warehouse_grouping | brand_grouping | total
---------------+-------+--------------------+----------------+-------
 San Francisco | NULL  |                  0 |              1 |   300
 San Jose      | NULL  |                  0 |              1 |   150
 Texas         | NULL  |                  0 |              1 |   450
 NULL          | NULL  |                  1 |              1 |   900
```

# 总结

- 使用 `PostgreSQL` 的 `GROUPING SETS` 在同一查询中创建多个分组。
- 如果列被聚合，`GROUPING` 函数返回1，否则返回0。

