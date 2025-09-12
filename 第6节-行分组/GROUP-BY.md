**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `GROUP BY` 根据一个或多个列的值将行分组。

# `PostgreSQL GROUP BY` 入门

`PostgreSQL` 的 `GROUP BY` 允许您根据一个或多个列的值将行分组。

以下是 `GROUP BY` 的语法：

```sql
SELECT column1, column2
FROM table_name
GROUP BY column1, column2;
```

在该语法中：

- 首先，在 `FROM` 子句中指定你想要从中选择数据的表名。
- 其次，提供一个或多个列，你希望在 `GROUP BY` 中对这些列的值进行分组。
- 第三，在 `SELECT` 语句中列出分组的列。

需要注意的是，`SELECT` 子句中的列必须出现在 `GROUP BY` 子句中。

如果在 `SELECT` 子句中指定了未出现在 `GROUP BY` 子句中的列，你将会遇到错误。

# `PostgreSQL GROUP BY` 基本示例

假设我们有以下 `inventories` 表：

| id | product_name | quantity | warehouse_id | brand_id |
|:----:|:----:|:----:|:----:|:----:|
| 1 | iPhone 15 | 55 | 1 | 1 |
| 2 | iPhone 14 Pro Max | 15 | 2 | 1 |
| 3 | iPhone 13 | 35 | 2 | 1 |
| 4 | Galaxy S24 | 50 | 1 | 2 |
| 5 | Galaxy Note 23 | 40 | 2 | 2 |
| 6 | Galaxy Z Fold 6 | 25 | 2 | 2 |
| 7 | Galaxy Z Flip 6 | 15 | 2 | 2 |
| 8 | Pixel 9 | 50 | 1 | 3 |
| 9 | Pixel 8 Pro | 25 | 2 | 3 |
| 10 | Pixel Fold | 60 | 1 | 3 |

创建表并填充示例数据的SQL脚本

```sql
CREATE TABLE warehouses(
   warehouse_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
   warehouse_name VARCHAR(255) NOT NULL UNIQUE 
);

CREATE TABLE brands(
   brand_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
   brand_name VARCHAR(255) NOT NULL UNIQUE   
);


CREATE TABLE inventories(
   id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
   product_name VARCHAR(255) NOT NULL,
   quantity INT NOT NULL,
   warehouse_id int not null,
   brand_id int not null,
   FOREIGN KEY (warehouse_id) REFERENCES warehouses(warehouse_id) ON DELETE CASCADE,
   FOREIGN KEY (brand_id) REFERENCES brands(brand_id) ON DELETE CASCADE
);



INSERT INTO warehouses (warehouse_name) 
VALUES ('San Jose'),
       ('San Francisco');


INSERT INTO brands (brand_name) 
VALUES ('Apple'),
('Samsung'),
('Google');



INSERT INTO inventories (product_name, quantity, warehouse_id, brand_id) 
VALUES 
('iPhone 15', 55, 1, 1),
('iPhone 14 Pro Max', 15,  2, 1),
('iPhone 13', 35, 2, 1),
('Galaxy S24', 50,  1, 2),
('Galaxy Note 23', 40,  2, 2),
('Galaxy Z Fold 6', 25, 2, 2),
('Galaxy Z Flip 6', 15, 2, 2),
('Pixel 9', 50,  1, 3),
('Pixel 8 Pro', 25, 2, 3),
('Pixel Fold', 60, 1, 3);
```

以下语句使用 `GROUP BY` 子句，按 `warehouse_id` 列中的值对 `products` 表中的行进行分组：

```sql
SELECT
  warehouse_id
FROM
  inventories
GROUP BY
  warehouse_id;
```

输出：

```sql
 warehouse_id
--------------
            2
            1
```

工作原理：

首先，`FROM` 子句开始检查来自 `inventories` 表的所有行。

其次，`GROUP BY` 子句会选择 `warehouse_id` 列中的唯一值。对于每个`warehouse_id` 值，`GROUP BY` 会将行聚合到一个组中。因此，我们有两个组，其`warehouse_id` 分别为 `1` 和 `2` ：

| warehouse_id | product_name | quantity | brand_id |
|:----:|:----:|:----:|:----:|
| 1 |  iPhone 15 | 55 | 1 |
|   | Galaxy S24 | 50 | 2 |
|   | Pixel 9 | 50 | 3 |
|   | Pixel Fold | 60 | 3 |
| 2 | Galaxy Note 23 | 40 | 2 |
|   | Galaxy Z Fold 6 | 25 | 2 |
|   | Galaxy Z Flip 6 | 15 | 2 |
|   | iPhone 14 Pro Max | 15 | 1 |
|   | iPhone 13 | 35 | 1 |
|   | Pixel 8 Pro | 25 | 3 |

最后，`SELECT` 语句选择分组列 `warehouse_id` ：

| warehouse_id |
|:----:|
| 1 |
| 2 |

当与聚合函数一起使用时，`GROUP BY` 子句会更有用。

# 聚合函数

聚合函数接收一组值，执行计算后返回单个值。

以下是最常用的聚合函数：

- `MIN` 函数返回一组数据中的最小值。
- `MAX` 函数返回一组数据中的最大值。
- `SUM` 函数返回一个集合的总和。
- `AVG` 函数返回一个集合的平均值。
- `COUNT` 函数返回一个集合中的项目数量。

# `MIN` 函数

以下语句使用 `MIN()` 语句查找 `inventories` 表中的最小数量：

```sql
SELECT
  MIN(quantity)
FROM
  inventories;
```

输出：

```sql
 min
-----
  15
```

查询的工作原理：

- 首先，`FROM` 子句会检查 `inventories` 表中的每一行。
- 其次，`MIN` 函数返回所有行中的最小数量。

# `MAX` 函数

以下示例使用 `MAX()` 函数来查找库存中某产品的最高数量：

```sql
SELECT
  MAX(quantity)
FROM
  inventories;
```

输出：

```sql
 max
-----
  60
```

# `SUM` 函数

以下 `SELECT` 语句使用 `SUM()` 函数计算 `inventories` 表中所有产品的总数量：

```sql
SELECT
  SUM(quantity)
FROM
  inventories;
```

输出：

```sql
 sum
-----
 370
```

# `AVG` 函数

以下语句使用 `AVG()` 函数计算 `inventories` 表中所有产品的平均数量：

```sql
SELECT
  AVG(quantity)
FROM
  inventories;
```

输出：

```sql
         avg
---------------------
 37.0000000000000000
```

# `COUNT` 函数

以下语句使用 `COUNT()` 函数返回 `inventories` 表中的行数：

```sql
SELECT
  COUNT(*)
FROM
  inventories;
```

输出：

```sql
 count
-------
    10
```

# 使用 `PostgreSQL` 的 `GROUP BY` 语句与聚合函数

以下是将 `GROUP BY` 子句与聚合函数结合使用的语法：

```sql
SELECT
  column1,
  aggregate_function (column2)
FROM
  table_name
GROUP BY
  column1;
```

在该语法中：

首先，`GROUP BY` 语句根据 `column1` 将表中的行分组。
其次，聚合函数执行计算并为每个组返回一个单一值：
第三，`SELECT` 子句返回分组列 `(column1)` 以及每个组的聚合值。
例如，以下展示了如何计算每个仓库的总数量：

```sql
SELECT
  warehouse_id,
  SUM(quantity)
FROM
  inventories
GROUP BY
  warehouse_id;
```

输出：

```sql
 warehouse_id | sum
--------------+-----
            2 | 155
            1 | 215
```

在这个示例中：

`FROM` 子句开始检查 `inventories` 表中的所有行。

`GROUP BY` 子句将库存中的所有行分为两组：

| warehouse_id | sum | product_name | quantity | brand_id |
|:----:|:----:|:----:|:----:|:----:|
|  1   | 215  |  iPhone 15  | 55 | 1 |
|      |      | Galaxy S24  | 50 | 2 |
|      |      | Pixel 9     | 50 | 3 |
|      |      | Pixel Fold  | 60 | 3 |
|  2   | 155  | Galaxy Note 23  | 40 | 2 |
|      |      | Galaxy Z Fold 6  | 25 | 2 |
|      |      | Galaxy Z Flip 6  | 15 | 2 |
|      |      | iPhone 14 Pro Max  | 15 | 1 |
|      |      | iPhone 13  | 35 | 1 |
|      |      | Pixel 8 Pro  | 25 | 2 |

然后，`SUM` 函数会计算每个组的数量总和。

`SELECT` 子句返回每个组的 `warehouse_id` 和总数量：

# 使用 `PostgreSQL` 的 `GROUP BY` 与 `INNER JOIN`

你可以使用连接来合并多个表的行，并使用 `GROUP BY` 子句将行分组。

例如，以下查询会返回每个仓库的名称以及产品的总数量：

```sql
SELECT
  warehouse_name,
  SUM(quantity)
FROM
  inventories
  INNER JOIN warehouses USING (warehouse_id)
GROUP BY
  warehouse_name;
```

输出：

```sql
 warehouse_name | sum
----------------+-----
 San Jose       | 215
 San Francisco  | 155
```

它的工作原理。

首先，`FROM` 会检查 `inventories` 表中的所有行。

接下来，`INNER JOIN` 会根据 `warehouse_id` 列中的值，将 `inventories` 表的行与 `warehouses` 表的行进行合并：


| id | product_name | quantity | warehouse_id | brand_id | warehouse_id | warehouse_name |
|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
| 1 | iPhone 15	| 55 | 1 | 1 | 1 | San Jose |
| 2 | iPhone 14 Pro Max	15 | 2 | 1 | 2  | San Francisco |
| 3 | iPhone 13	35 | 2 | 1 | 2 | San Francisco |
| 4 | Galaxy S24	| 50 | 1 | 2 | 1 | San Jose |
| 5 | Galaxy Note 23 | 40 | 2 | 2 | 2 | San Francisco |
| 6 | Galaxy Z Fold 6 | 25 | 2 | 2 | 2 | San Francisco |
| 7 | Galaxy Z Flip 6	| 15 | 2 | 2 | 2 | San Francisco |
| 8 | Pixel 9	50 | 1 | 3 | 1 | San Jose |
| 9 | Pixel 8 Pro	25 | 2 | 3 | 2 | San Francisco |
| 10 | Pixel Fold	60 | 1 | 3 | 1 | San Jose |

然后，`GROUP BY` 组按 `warehouse_name` 中的值合并行：

| warehouse_name | product_name | quantity | warehouse_id  | brand_id |
|:----:|:----:|:----:|:----:|:----:|
| San Jose | 1 | iPhone 15 | 55 | 1 | 1 |
|          | 2 | Galaxy S24 | 50 | 1 | 2 |
|          | 8 | Pixel 9 | 50 | 1 | 3 |
|          | 10 | Pixel Fold | 60 | 1 | 3 |
| San Francisco | 2 | iPhone 14 Pro Max | 15 | 2 | 1 |
|               | 3 | iPhone 13 | 35 | 2 | 1 |
|               | 5 | Galaxy Note 23 | 40 | 2 | 2 |
|               | 6 | Galaxy Z Fold 6 | 25 | 2 | 2 |   
|               | 7 | Galaxy Z Flip 6 | 15 | 2 | 2 |
|               | 9 | Pixel 8 Pro | 25 | 2 | 3 |

之后， `SUM` 函数会计算每个组的总数：

| warehouse_name | sum | id | product_name | quantity | warehouse_id | brand_id |
|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
| San Jose | 215 | 1 | iPhone 15 | 55 | 1 | 1 |
|          |     | 4 | Galaxy S24 | 50 | 1 | 2 |
|          |     | 8 | Pixel 9 | 50 | 1 | 3 |
|          |     | 10 | Pixel Fold | 60 | 1 | 3 |
|   San Francisco | 155 | 2 | iPhone 14 Pro Max | 15 | 2 | 1 |
|                 |     | 3 | iPhone 13 | 35 | 2 | 1 |
|                 |     | 5 | Galaxy Note 23 | 40 | 2 | 2 |
|                 |     | 6 | Galaxy Z Fold 6 | 25 | 2 | 2 |
|                 |     | 7 | Galaxy Z Flip 6 | 15 | 2 | 2 |
|                 |     | 9 | Pixel 8 Pro | 25 | 2 | 3 |

最后，`SELECT` 子句返回仓库名称和每个仓库的总数量：

| warehouse_name | sum |
| San Jose | 215 |
| San Francisco | 200 |

# 按多列分组

以下语句使用 `GROUP BY` 子句按 `warehouse_id` 和 `brand_id` 对 `inventories` 表中的行进行分组：

```sql
SELECT
  warehouse_id,
  brand_id,
  SUM(quantity)
FROM
  inventories
GROUP BY
  warehouse_id,
  brand_id;
```

输出：

```sql
 warehouse_id | brand_id | sum
--------------+----------+-----
            1 |        1 |  55
            2 |        3 |  25
            1 |        3 | 110
            2 |        2 |  80
            1 |        2 |  50
            2 |        1 |  50
```

在这个示例中，`warehouse_id` 和 `brand_id` 列中的值组合形成一个组。`SUM()` 函数返回每个组的产品数量总和。

# 总结

- 使用 `GROUP BY` 子句将 `SQL` 查询中的行按一个或多个列分组为汇总行。
- 将聚合函数与 `GROUP BY` 子句结合使用，以计算每个组的值。
- 使用 `JOIN` 和 `GROUP BY` 来合并多个表中的行并将行分组。