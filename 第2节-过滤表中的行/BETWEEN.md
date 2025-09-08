**摘要**: 在本教程中，您将学习如何在 `WHERE` 子句中使用 `PostgreSQL` 的 `BETWEEN` 运算符来检查某个值是否在两个值之间。

# `PostgreSQL BETWEEN` 运算符

BETWEEN运算符是一种比较运算符，如果某个值介于两个值之间，则返回true。

以下是 `BETWEEN` 运算符的语法:

```sql
value BETWEEN low AND high
```

如果 `value` 在 `low` 和 `high` 之间，则 `BETWEEN` 运算符返回true。

```sql
low <= value <= high
```

在SQL中，`BETWEEN` 运算符是书写以下表达式的一种简写形式:

```sql
value >= low AND value <= high
```

因此，`BETWEEN` 运算符简化了上述范围测试。

实际上，你会在 `WHERE` 子句中使用 `BETWEEN` 运算符，根据一系列值来筛选行。

# `PostgreSQL BETWEEN` 运算符示例

假设我们有以下 `inventories` 表:

```sql
CREATE TABLE inventories (
  name VARCHAR(255),
  brand VARCHAR(50),
  quantity INT,
  price DECIMAL(19, 2)
);

INSERT INTO
  inventories (name, brand, quantity, price)
VALUES
  ('iPhone 14 Pro', 'Apple', 10, 999.99),
  ('Galaxy S23 Ultra', 'Samsung', 15, 1199.99),
  ('Pixel 7 Pro', 'Google', 8, 899.99),
  ('Xperia 1 IV', 'Sony', 7, 1299.99);
```

数据:

```sql
       name       |  brand  | quantity |  price
------------------+---------+----------+---------
 iPhone 14 Pro    | Apple   |       10 |  999.99
 Galaxy S23 Ultra | Samsung |       15 | 1199.99
 Pixel 7 Pro      | Google  |        8 |  899.99
 Xperia 1 IV      | Sony    |        7 | 1299.99
```

以下示例使用 `BETWEEN` 运算符查找 `inventories` 表中价格在 `899.99` 到 `999.99` 之间的产品:

```sql
SELECT
  name,
  price
FROM
  inventories
WHERE
  price BETWEEN 899.99 AND 999.99;
```

输出:

```sql
     name      | price
---------------+--------
 iPhone 14 Pro | 999.99
 Pixel 7 Pro   | 899.99
```

# `NOT BETWEEN` 运算符

`NOT` 运算符对 `BETWEEN` 运算符取反:

```sql
value NOT BETWEEN low AND high
```

如果值小于 `low` 或 `greater` 高值，则 `NOT BETWEEN` 返回true:

```sql
value < low OR value > high
```

例如，以下语句使用 `NOT BETWEEN` 运算符来查找价格不在 `899.99` 到 `999.99` 之间的产品:

```sql
SELECT
  name,
  price
FROM
  inventories
WHERE
  price NOT BETWEEN 899.99 AND 999.99;
```

输出:

```sql
       name       |  price
------------------+---------
 Galaxy S23 Ultra | 1199.99
 Xperia 1 IV      | 1299.99
```

# 总结

- 使用 `PostgreSQL` 的 `BETWEEN` 运算符筛选介于低值和高值之间的值。
- 使用 `NOT` 运算符对 `BETWEEN` 运算符进行取反。
