**摘要**：在本教程中，您将学习如何使用 `PostgreSQL DISTINCT ON` 子句从表中选择不同的行。

# `PostgreSQL SELECT DISTINCT ON` 语句入门

在 `PostgreSQL` 中，`SELECT DISTINCT ON` 子句允许您为 `ON` 子句定义的每个组选择一行。

下面是 `DISTINCT ON` 子句的语法:

```sql
SELECT DISTINCT ON (column1), column2, column3
FROM table_name
ORDER BY column1, column2, column3;
```

在此语法中：

首先，在 `ON` 关键字的括号内指定一列 （`column1`）。ON 关键字将按 `column1` 中的值将 `table_name` 中的行分组为不同的组。`SELECT` 子句可以包括其他列（ `column2` 、`column3` ）或 `expressions` 。

其次，将 `ON` 子句中定义的列作为 `ORDER BY` 子句中的第一列提供。`ORDER BY` 子句按 `column1` 对每个不同组中的行进行排序，并仅保留第一行。

> 请注意，`SELECT DISTINCT ON` 是 `PostgreSQL` 对 `SQL` 标准的扩展。因此，它可能在其他数据库系统中不可用。

# `PostgreSQL SELECT DISTINCT ON` 语句示例

假设您有以下 `inventories` 表，其中包括产品名称、品牌、仓库和数量。


| id  | product_name       | brand   | warehouse     | quantity |
|-----|--------------------|---------|---------------|----------|
| 1   | iPhone 16          | Apple   | San Jose      | 50       |
| 2   | iPhone 16          | Apple   | San Francisco | 30       |
| 3   | iPhone 16 Pro      | Apple   | San Jose      | 40       |
| 4   | iPhone 16 Pro      | Apple   | San Francisco | 20       |
| 5   | iPhone 16 Pro Max  | Apple   | San Francisco | 25       |
| 6   | Galaxy S22         | Samsung | San Jose      | 50       |
| 7   | Galaxy S22         | Samsung | San Francisco | 30       |
| 8   | Galaxy S22 Ultra   | Samsung | San Francisco | 20       |
| 9   | Galaxy Z Fold 4    | Samsung | San Jose      | 55       |
| 10  | Galaxy Z Fold 4    | Samsung | San Francisco | 35       |

用于创建库存表并将数据插入其中的 SQL 脚本

```sql
CREATE TABLE inventories(
   id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
   product_name VARCHAR(50) NOT NULL,
   brand VARCHAR(50) NOT NULL,
   warehouse VARCHAR(50) NOT NULL,
   quantity INT NOT NULL
);

INSERT INTO inventories (product_name, brand, warehouse, quantity) 
VALUES
('iPhone 16', 'Apple', 'San Jose', 50),
('iPhone 16', 'Apple', 'San Francisco', 30),
('iPhone 16 Pro', 'Apple', 'San Jose', 40),
('iPhone 16 Pro', 'Apple', 'San Francisco', 20),
('iPhone 16 Pro Max', 'Apple', 'San Francisco', 25),
('Galaxy S22', 'Samsung', 'San Jose', 50),
('Galaxy S22', 'Samsung', 'San Francisco', 30),
('Galaxy S22 Ultra', 'Samsung', 'San Francisco', 20),
('Galaxy Z Fold 4', 'Samsung', 'San Jose', 55),
('Galaxy Z Fold 4', 'Samsung', 'San Francisco', 35);
```

以下语句使用 `SELECT DISTINCT ON` 子句为每个产品选择产品名称、品牌和最高库存数量：

```sql
SELECT DISTINCT
  ON (product_name) product_name,
  brand,
  quantity
FROM
  inventories
ORDER BY
  product_name,
  quantity DESC;
```

输出：

```sql
   product_name    |  brand  | quantity
-------------------+---------+----------
 Galaxy S22        | Samsung |       50
 Galaxy S22 Ultra  | Samsung |       20
 Galaxy Z Fold 4   | Samsung |       55
 iPhone 16         | Apple   |       50
 iPhone 16 Pro     | Apple   |       40
 iPhone 16 Pro Max | Apple   |       25
```

在此示例中：

首先，`ON` 子句按产品名称将 `inventories` 表中的行分为六组：

```sql
ON (product_name) product_name
```

在此子句中，我们将 `product_name` 指定为列别名，以便在 `ORDER BY` 子句中使用以进行排序。

其次，`ORDER BY` 子句按产品名称和数量从高到低对每组中的行进行排序，并选择每组中的第一行。

如果您想选择产品名称、品牌和最低库存数量，您可以更改 `ORDER CLAUSE` ，优先放置数量最低的产品：

```sql
SELECT DISTINCT
  ON (product_name) product_name,
  brand,
  quantity
FROM
  inventories
ORDER BY
  product_name,
  quantity;
```

输出：

```sql
   product_name    |  brand  | quantity
-------------------+---------+----------
 Galaxy S22        | Samsung |       30
 Galaxy S22 Ultra  | Samsung |       20
 Galaxy Z Fold 4   | Samsung |       35
 iPhone 16         | Apple   |       30
 iPhone 16 Pro     | Apple   |       20
 iPhone 16 Pro Max | Apple   |       25
```

# 总结

- 使用 `SELECT DISTINCT ON` 将行分组到不同的组中，并保留每个组中的第一行。
- 使用 `ORDER BY` 子句定义要保留在每个组中的行，并将其包含在最终结果集中。