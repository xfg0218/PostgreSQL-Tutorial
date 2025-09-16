**摘要**：在本教程中，您将学习如何使用 `PostgreSQL SELECT DISTINCT DISTINCT` 子句从表中检索唯一值。

# `PostgreSQL SELECT DISTINCT` 入门

`SELECT DISTINCT` 语句从表中检索唯一值。下面是 `SELECT DISTINCT` 子句的语法：

```sql
SELECT DISTINCT column1
FROM table_name;
```

在此语法中，`SELECT DISTINCT` 子句使用 `table_name` 的 `column1` 中的值来评估行的唯一性并返回唯一行。

# `PostgreSQL SELECT DISTINCT` 子句示例

假设您有以下 `inventories` 表，其中包括产品名称、品牌、仓库和数量：

用于创建库存表并将数据插入其中的 `SQL` 脚本

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


以下示例使用 `SELECT` 语句从 `inventories` 表中检索所有产品名称：

```sql
SELECT
  product_name
FROM
  inventories
ORDER BY
  product_name;
```

输出：

```sql
   product_name
-------------------
 Galaxy S22
 Galaxy S22
 Galaxy S22 Ultra
 Galaxy Z Fold 4
 Galaxy Z Fold 4
 iPhone 16
 iPhone 16
 iPhone 16 Pro
 iPhone 16 Pro
 iPhone 16 Pro Max
```

结果集包含许多重复的产品名称。

要从结果集中删除重复的名称，可以使用 `SELECT DISTINCT` 子句：

```sql
SELECT DISTINCT
  product_name
FROM
  inventories
ORDER BY
  product_name;
```

输出：

```sql
   product_name
-------------------
 Galaxy S22
 Galaxy S22 Ultra
 Galaxy Z Fold 4
 iPhone 16
 iPhone 16 Pro
 iPhone 16 Pro Max
```

# 将 `SELECT DISTINCT` 子句应用于多列

`SELECT DISTINCT` 子句可以接受多列。

下面是将 `SELECT DISTINCT` 子句与两列一起使用的语法：

```sql
SELECT DISTINCT column1, column2
FROM table_name;
```

在这种情况下，`SELECT DISTINCT` 子句使用这些列中的值组合来评估重复项。

例如，以下语句使用 `SELECT DISTINCT` 子句从 `inventories` 表中检索不同的产品名称和品牌：

```sql
SELECT DISTINCT product_name, brand
FROM inventories;
```

输出：

```sql
   product_name    |  brand
-------------------+---------
 iPhone 16         | Apple
 iPhone 16 Pro Max | Apple
 Galaxy S22 Ultra  | Samsung
 iPhone 16 Pro     | Apple
 Galaxy S22        | Samsung
 Galaxy Z Fold 4   | Samsung
```

输出显示 `SELECT DISTINCT` 子句返回唯一的产品名称和品牌。

# `SELECT DISTINCT` 与 `GROUP BY` 子句

`SELECT DISTINCT` 通过从结果集中消除重复行来从表中检索唯一行。

`GROUP BY` 子句根据一列或多列的值将行分组为组，并将聚合函数应用于每个组。没有聚合函数的 `GROUP BY` 子句与 `SELECT DISTINCT` 子句具有相同的效果。

例如，以下语句使用 `GROUP BY` 子句从库存表中选择不同的产品名称：

```sql
SELECT
  product_name
FROM
  inventories
GROUP BY
  product_name
ORDER BY
  product_name;
```

输出：

```sql
   product_name
-------------------
 Galaxy S22
 Galaxy S22 Ultra
 Galaxy Z Fold 4
 iPhone 16
 iPhone 16 Pro
 iPhone 16 Pro Max
```

它返回与以下使用 `DISTINCT` 子句的查询相同的结果集：

```sql
SELECT DISTINCT
  product_name
FROM
  inventories
ORDER BY
  product_name;
```

# 总结

使用 `SELECT DISTINCT` 子句从表中检索唯一行。