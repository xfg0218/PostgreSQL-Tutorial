**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `CUBE` 来生成指定列的所有可能聚合。

# `PostgreSQL CUBE` 简介

`GROUP BY` 语句允许你对行进行分组，并计算单个分组集的聚合结果。

要计算一组列的所有可能组合的聚合结果，您可以使用带有 `CUBE` 选项的 `GROUP BY` 子句。

以下是结合 `CUBE` 的 `GROUP BY` 语法：

```sql
SELECT
  column1,
  column2,
  aggregate_function (column3)
FROM
  table_name
GROUP BY
  CUBE (column1, column2);
```

在该语法中，`CUBE` 将生成以下分组：

- `(column1, column2)` ：按 `column1` 和 `column2` 进行单独分组。
- `(column1, NULL)` ： `column2` 中值的分组。
- `(NULL, column2)` ：按 `column1` 中的值分组。
- `(NULL, NULL)` ：所有列的分组。

# `PostgreSQL CUBE` 示例 

我们将使用 `inventory_reports` 表来演示 `GROUP BY CUBE` ：

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

# 使用 `PostgreSQL CUBE` 处理单个列

以下查询使用 `GROUP BY` 子句来计算每个品牌的总数量：

```sql
SELECT
  brand,
  SUM(quantity) total_quantity
FROM
  inventory_reports
GROUP BY
  brand;
```

输出：

```sql
  brand  | total_quantity
---------+----------------
 Samsung |            300
 Apple   |            600
```

若要添加一行来计算所有品牌的总数量，您可以按如下方式使用 `GROUP BY CUBE`：

```sql
SELECT
  brand,
  SUM(quantity) total_quantity
FROM
  inventory_reports
GROUP BY
  CUBE (brand)
ORDER BY
  brand NULLS LAST;
```

输出：

```sql
  brand  | total_quantity
---------+----------------
 Apple   |            600
 Samsung |            300 
 NULL    |            900 -> grand total
```

在这个示例中，`CUBE` 生成两个分组：

- `(brand)` ：按品牌计算数量总和。
- `()` ：计算所有品牌的数量总和。

# 使用多列的 `CUBE` 

以下查询使用 `GROUP BY CUBE` 生成小计和总计：

```sql
SELECT
  brand,
  warehouse,
  SUM(quantity) total_quantity
FROM
  inventory_reports
GROUP BY
  CUBE (brand, warehouse)
ORDER BY
  brand NULLS LAST,
  warehouse NULLS LAST;
```

输出：

```sql
  brand  |   warehouse   | total_quantity
---------+---------------+----------------
 Apple   | San Francisco |            200
 Apple   | San Jose      |            100
 Apple   | Texas         |            300
 Apple   | NULL          |            600  
 Samsung | San Francisco |            100
 Samsung | San Jose      |             50
 Samsung | Texas         |            150
 Samsung | NULL          |            300  
 NULL    | San Francisco |            300  
 NULL    | San Jose      |            150  
 NULL    | Texas         |            450  
 NULL    | NULL          |            900
```

在这个示例中，`CUBE` 生成了四个分组集：

- `(brand, warehouse)` ：按品牌和仓库计算总数量。
- `(brand, NULL)` ：计算每个品牌的小计，汇总该品牌的所有仓库。`NULL` 位于 `warehouse` 列中。
- `(NULL, warehouse)` ：计算每个仓库的小计，汇总所有品牌的数据。`NULL` 位于 `brand` 列中。
- `(NULL, NULL)` ：计算总计，将所有仓库和品牌相加。

# `GROUPING SETS`、`ROLLUP` 和 `CUBE` 之间的区别

以下表格说明了 `GROUPING SETS` 、`ROLLUP` 和 `CUBE` 之间的区别：

| Option | Description |
|:----:|:----:|
| CUBE | 生成指定列的所有可能组合。 |
| ROLLUP | 生成 hierarchical summaries |
| GROUPING SETS | 生成指定的分组集，而非生成所有可能的组合。 |

# 总结

使用 `PostgreSQL` 的 `CUBE` 通过计算指定列的所有可能聚合来创建多维汇总。
