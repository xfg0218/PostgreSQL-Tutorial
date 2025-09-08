**摘要** : 在本教程中，你将学习如何使用 `PostgreSQL` 的 `UPDATE` 语句来更新表中一行或多行的数据。

#  `PostgreSQL UPDATE` 语句简介

要更新表中的数据，需使用 `PostgreSQL` 的 `UPDATE` 语句。

以下是 `UPDATE` 语句的语法:

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```

在以下语法中:

- 首先，在 `UPDATE` 关键字后指定你想要更新数据的表的名称。
- 其次，在 `SET` 子句中提供列名和新值。
- 第三，在 `WHERE` 子句中指定一个条件，以确定要更新哪些行。`UPDATE` 语句只会更新满足该条件的行。

`WHERE` 子句是可选的。如果省略WHERE子句，`UPDATE` 语句将更新所有行的列:

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...;
```

忘记 `WHERE` 子句可能会导致意外的更改，因为 `UPDATE` 语句会更新表中的每一行。

为避免这种风险，你应该始终包含一个 `WHERE` 子句，以精确瞄准你想要更新的行。

`UPDATE` 语句会向客户端返回更新的行数。

# 设置示例表

我们将更新 `inventories` 表中的数据:

```sql
CREATE TABLE inventories (
  name VARCHAR(255),
  brand VARCHAR(50),
  quantity INT,
  price DECIMAL(19, 2)
);


INSERT INTO inventories (name, brand, quantity, price)
VALUES ('iPhone 14 Pro', 'Apple', 10, 999.99),
       ('Galaxy S23 Ultra', 'Samsung', 15, 1199.99),
       ('Pixel 7 Pro', 'Google', 8, 899.99),
       ('Xperia 1 IV', 'Sony', 7, 1299.99);
```

# 更新表中一行的示例

以下示例使用 `UPDATE` 语句将名称为 "iPhone 14 Pro" 的产品的数量修改为30:

```sql
UPDATE inventories
SET
  quantity = 30
WHERE
  name = 'iPhone 14 Pro';
```

输出:

```sql
UPDATE 1
```

输出表明 `UPDATE` 语句成功更新了一行。要验证更新结果，您可以从 `inventories` 表中检索数据:

```sql
SELECT
  name,
  quantity
FROM
  inventories
WHERE
  name = 'iPhone 14 Pro';
```

输出:

```sql
     name      | quantity
---------------+----------
 iPhone 14 Pro |       30
```

# 更新表中的所有行

以下示例使用 `UPDATE` 语句，通过将价格乘以 `0.9`（即`90%`），将所有产品的价格降低`10%`:

```sql
UPDATE inventories
SET
  price = price * 0.9;
```

输出:

```sql
UPDATE 4
```

在这个示例中,我们没有使用WHERE子句,因此该语句会更新所有行。输出结果显示,`UPDATE` 语句更新了 `inventories` 表中的四行数据。

以下 `SELECT` 语句从 `inventories` 表中查询所有行:

```sql
SELECT
  name,
  price
FROM
  inventories;
```

输出:

```sql
       name       |  price
------------------+---------
 Galaxy S23 Ultra | 1079.99
 Pixel 7 Pro      |  809.99
 Xperia 1 IV      | 1169.99
 iPhone 14 Pro    |  899.99
```

输出表明该语句已成功更新价格。

# 返回更新的行

`UPDATE` 语句提供了 `RETURNING` 子句，用于返回已更新的行。

```sql
UPDATE table_name
SET
  column1 = value1,
  column2 = value2
WHERE
  condition 
RETURNING 
   column1, column2;
```

`RETURNING` 子句返回包含该子句中指定列的已更新行。

如果您想返回更新行的所有列，可以在 `RETURNING` 子句中使用星号 `(*)` 简写。

这是一种便捷的方式，可以返回所有列，而无需逐个列出它们。

```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition
RETURNING *;
```

以下示例使用 `UPDATE` 语句来更新 `Pixel 7 Pro` 产品 `750` 并返回更新的行：

```sql
UPDATE inventories
SET
  price = 750
WHERE 
  name = 'iPhone 14 Pro'
RETURNING *;
```

输出:

```sql
     name      | brand | quantity | price
---------------+-------+----------+--------
 iPhone 14 Pro | Apple |       30 | 750.00
```

# 总结

- 使用 `UPDATE` 语句更新表中的数据。
- 省略 `WHERE` 子句将更新表中的所有行。
- 使用 `RETURNING` 子句返回更新后的行。
