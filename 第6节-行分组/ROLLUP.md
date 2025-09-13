**摘要**：在本教程中，您将学习如何使用 `PostgreSQL` 的 `ROLLUP` 来创建多个聚合级别。

# `PostgreSQL ROLLUP` 概述

在 `PostgreSQL` 中，`GROUP BY` 子句将行分组为多个组，您可以对每个组应用聚合函数。但是，`GROUP BY` 子句只能生成单级聚合。 

要生成多个聚合级别，需使用 `GROUP BY` 子句的 `ROLLUP` 选项。

以下是带有 `ROLLUP` 选项的 `GROUP BY` 的语法：

```sql
SELECT
  column1,
  column2,
  aggregate_function(column3)
FROM
  table_name
GROUP BY
  ROLLUP(column1, column2);
```

在此语法中，`ROLLUP(column1, column2)` 会生成三个分组：

1. `(column1, column2)` ：包含按 `column1` 和 `column2` 的值聚合的数据的常规分组。
2. `(column1, NULL)` ：按 `column1` 的小计。
3. `(NULL, NULL)` ：总计。

`ROLLUP` 假设 `column1` 和 `column2` 之间存在层次关系：

```sql
column1 > column2
```

`ROLLUP` 可以帮助按层次结构生成小计行和总计行。

# `PostgreSQL ROLLUP` 示例

我们将使用 `inventory_reports` 表来演示 `ROLLUP` 的工作原理。

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

# 使用 `ROLLUP` 处理单个列

以下查询使用 `GROUP BY` 子句计算每个仓库的总数量：

```sql
SELECT
  warehouse,
  SUM(quantity) AS total_quantity
FROM
  inventory_reports
GROUP BY
  warehouse
ORDER BY
  warehouse;
```

输出：

```sql
   warehouse   | total_quantity
---------------+----------------
 San Francisco |            300
 San Jose      |            150
 Texas         |            450
```

若要包含总计行，您可以按如下方式使用 `ROLLUP`：

```sql
SELECT
  warehouse,
  SUM(quantity) AS total_quantity
FROM
  inventory_reports
GROUP BY
  ROLLUP(warehouse)
ORDER BY
  warehouse;
```

输出：

```sql
   warehouse   | total_quantity
---------------+----------------
 San Francisco |            300
 San Jose      |            150
 Texas         |            450
 NULL          |            900   -> grand total
```

在这个示例中，`ROLLUP(warehouse)` 多添加了一行，其中包含所有仓库的总数量。

# 使用 `ROLLUP` 处理多列

以下查询使用 `GROUP BY` 子句按仓库和品牌生成总数量：

```sql
SELECT
  warehouse,
  brand,
  SUM(quantity) AS total_quantity
FROM
  inventory_reports
GROUP BY
  warehouse,
  brand
ORDER BY
  warehouse,
  brand;
```

输出：

```sql
   warehouse   |  brand  | total_quantity
---------------+---------+----------------
 San Francisco | Apple   |            200
 San Francisco | Samsung |            100
 San Jose      | Apple   |            100
 San Jose      | Samsung |             50
 Texas         | Apple   |            300
 Texas         | Samsung |            150
```

输出结果没有显示按仓库的小计以及所有仓库的总数量。

要生成小计和总计，您可以按如下方式使用 `ROLLUP` 选项：

```sql
SELECT
  warehouse,
  brand,
  SUM(quantity) AS total_quantity
FROM
  inventory_reports
GROUP BY
  ROLLUP(warehouse, brand)
ORDER BY
  warehouse NULLS LAST,
  brand NULLS LAST;
```

输出：

```sql
   warehouse   |  brand  | total_quantity
---------------+---------+----------------
 San Francisco | Apple   |            200
 San Francisco | Samsung |            100
 San Francisco | NULL    |            300   --> subtotal
 San Jose      | Apple   |            100
 San Jose      | Samsung |             50
 San Jose      | NULL    |            150   --> subtotal
 Texas         | Apple   |            300
 Texas         | Samsung |            150
 Texas         | NULL    |            450   --> subtotal
 NULL          | NULL    |            900   --> grand total
```

在本示例中，`ROLLUP(warehouse, brand)` ：

- 按仓库和品牌生成总数量，这与之前不使用 `ROLLUP` 选项的示例相同。
- 为每个仓库添加一个小计行 （ `brand` 列中为 `NULL` ）。
- 添加一个总计行（在 `warehouse` 和 `brand` 列中均为 `NULL` ）。

如果你改变 `warehouse` 和 `brand` 列在 `ROLLUP` 中的顺序，结果集将会不同。例如：

```sql
SELECT
  brand,
  warehouse,
  SUM(quantity) AS total_quantity
FROM
  inventory_reports
GROUP BY
  ROLLUP(brand, warehouse)
ORDER BY
  brand NULLS LAST,
  warehouse NULLS LAST;
```

现在，`ROLLUP` 采用以下层级结构：

```sql
brand > warehouse
```

输出：

```sql
  brand  |   warehouse   | total_quantity
---------+---------------+----------------
 Apple   | San Francisco |            200
 Apple   | San Jose      |            100
 Apple   | Texas         |            300
 Apple   | NULL          |            600  -> subtotal
 Samsung | San Francisco |            100
 Samsung | San Jose      |             50
 Samsung | Texas         |            150
 Samsung | NULL          |            300  -> subtotal
 NULL    | NULL          |            900  -> total
```

# 总结

- 使用 `ROLLUP` 生成多个聚合级别。

