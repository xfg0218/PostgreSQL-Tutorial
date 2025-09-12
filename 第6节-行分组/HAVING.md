**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `HAVING` 子句来筛选由 `GROUP BY` 子句返回的分组。

# `PostgreSQL HAVING` 语句入门

`HAVING` 子句是一个强大的工具，它允许你指定一个条件来过滤 `GROUP BY` 语句返回的分组。

以下是 `HAVING` 子句的语法：

```sql
SELECT column1, column2
FROM table_name
GROUP BY column1, column2
HAVING condition;
```

在以下语法中：

- 首先，在 `FROM` 子句中指定你想要检索行的表名。
- 其次，在 `GROUP BY` 子句中定义一个或多个用于分组的列。
- 第三，在 `HAVING` 子句中提供一个条件，用于过滤 `GROUP BY` 子句返回的分组。如果该条件为真，`PostgreSQL` 会将该分组包含在结果集中。
- 最后，在 `SELECT` 子句中指定你想要包含在最终结果集中的列（或表达式）。

`PostgreSQL` 按以下顺序评估子句：

- `FROM`
- `GROUP BY`
- `HAVING`
- `SELECT`

由于 `PostgreSQL` 会在 `SELECT` 子句之前计算 `HAVING` 子句，因此列别名在 `HAVING` 子句中无法访问。

与根据单行筛选行的 `WHERE` 子句不同，`HAVING` 子句根据 `GROUP BY` 子句的结果对组进行筛选。

这意味着 `PostgreSQL` 在 `HAVING` 子句，从而允许您根据条件对分组进行筛选。

# 创建示例表

为了演示，我们将使用以下 `inventories` 表：

示例表:

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

# `PostgreSQL HAVING` 语句示例

以下语句使用 `GROUP BY` 子句来提供每个仓库的产品数量：

```sql
SELECT
  warehouse_id,
  SUM(quantity)
FROM
  inventories
GROUP BY
  warehouse_id;
```

它返回两行，包含仓库ID和数量。

以下语句使用 `HAVING` 子句仅检索产品数量大于 `200` 的仓库：

```sql
SELECT
  warehouse_id,
  SUM(quantity) qty
FROM
  inventories
GROUP BY
  warehouse_id
HAVING
  SUM(quantity) > 200;
```

输出：

```sql
 warehouse_id | qty
--------------+-----
            1 | 215
```

`PostgreSQL` 在 `SELECT` 子句之后计算 `HAVING` 子句；你不能在 `HAVING` 子句中访问 `qty` 列的别名。

它的工作原理。

首先，`GROUP BY` 子句将 `inventories` 表中的汇总行分为两组：

| warehouse_id | sum | product_name | quantity | brand_id |
|:----:|:----:|:----:|:----:|:----:|
| 1    | 215  | iPhone 15 | 55 | 1 |
|      |      | Galaxy S24 | 50 | 2 |
|      |      | Pixel 9 | 50 | 3 |
|      |      | Pixel Fold | 60 | 3 |
|  2   | 155  | Galaxy Note 23 | 40 | 2 |
|      |      | Galaxy Z Fold 6 | 25 | 2 |
|      |      | Galaxy Z Flip 6 | 15 | 2 |
|      |      | iPhone 14 Pro Max | 15 | 1 |
|      |      | iPhone 13 | 35 | 1 |
|      |      | Pixel 8 Pro | 25 | 2 |

其次，`HAVING` 子句根据总数量对分组进行筛选，只包含使条件成立的分组：

| warehouse_id | sum | product_name | quantity | brand_id |
|:----:|:----:|:----:|:----:|:----:|
|   1  |  215 | iPhone 15 | 55 | 1 |
|      |      | Galaxy S24 | 50 | 2 |
|      |      | Pixel 9 | 50 | 3 |
|      |      | Pixel Fold | 60 | 3 |

第三，`SELECT` 语句检索 `warehouse_id` 和 `sum` 列，并赋予列别名：

| warehouse_id | qty |
|:----:|:----:|
|   1  |  215 |

# `PostgreSQL` 中带有复杂条件的 `HAVING` 子句

以下示例使用 `HAVING` 子句返回每个仓库和品牌组合的产品总数量，且仅包含品牌1和品牌2，并且这些品牌的数量需超过50：

```sql
SELECT
  warehouse_id,
  brand_id,
  SUM(quantity)
FROM
  inventories
GROUP BY
  warehouse_id,
  brand_id
HAVING
  brand_id IN (1, 2)
  AND SUM(quantity) > 50;
```

输出：

```sql
 warehouse_id | brand_id | sum
--------------+----------+-----
            1 |        1 |  55
            2 |        2 |  80
```

它的工作原理。

首先，`GROUP BY` 按仓库和品牌返回产品总数量：

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

其次，`HAVING` 子句通过仅包含 `brand_id` 为 `1` 和 `2` 且产品数量大于 `50` 的组来对组进行筛选：

```sql
 warehouse_id | brand_id | sum
--------------+----------+-----
            1 |        1 |  55
            2 |        2 |  80
```

# 总结

- 使用 `PostgreSQL` 的 `HAVING` 子句来筛选 `GROUP BY` 子句返回的分组。






