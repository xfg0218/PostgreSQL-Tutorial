**摘要**: 在本教程中，你将学习如何在 `WHERE` 子句中使用 `PostgreSQL` 的 `IN` 运算符来筛选位于值列表中的行。

# `PostgreSQL IN` 运算符

如果某个值在一组值中,`IN` 运算符会返回 `true` 。

以下是 `IN` 运算符的语法:

```sql
value IN (value1, value2, ...)
```

在此语法中:

- 首先,在 `IN` 运算符的左侧指定你要检查的值。
- 其次,列出你想要验证的值,看该值是否等于其中的任意一个。

该值可以是整数、字符串、日期等。

通常，你会在 `SELECT` 语句的 `WHERE` 子句中使用IN来筛选表中的行:

```sql
WHERE value IN (value1, value2, ...)
```

`IN` 运算符与使用`OR`运算符进行的以下比较等效:

```sql
value = value1 OR value = value2 OR ...
```

然而,`IN` 运算符比一长串比较表达式更简洁易读。

# `PostgreSQL IN` 运算符示例

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

以下语句使用 `IN` 运算符检索库存数量为7或8的产品:

```sql
SELECT
  name,
  quantity
FROM
  inventories
WHERE
  quantity IN (7, 8);
```

输出:

```sql
    name     | quantity
-------------+----------
 Pixel 7 Pro |        8
 Xperia 1 IV |        7
```

输出返回了两款产品 `Pixel 7 Pro` 和 `Xperia 1 IV`,数量分别为8和7。

以下示例使用 `IN` 运算符检索名称在预定义名称列表中的产品:

```sql
SELECT
  name,
  price
FROM
  inventories
WHERE
  name IN ('iPhone 14 Pro', 'Pixel 7 Pro');
```

输出:

```sql
     name      | price
---------------+--------
 iPhone 14 Pro | 999.99
 Pixel 7 Pro   | 899.99
```

# `PostgreSQL NOT IN` 运算符

`NOT` 运算符对 `IN` 运算符进行取反:

```sql
value NOT IN (value1, value2, ...)
```

如果某个值不在值列表 `（value1、value2……) `中，`NOT IN` 运算符会返回 true 。

`NOT IN` 运算符等价于以下表达式:

```sql
value != value1 AND value != value2 AND ...
```

以下示例使用 `NOT IN` 运算符检索 `inventories` 中名称不在列表中的产品:

```sql
SELECT
  name,
  price
FROM
  inventories
WHERE
  name NOT IN ('iPhone 14 Pro', 'Pixel 7 Pro');
```

# 总结

- 使用 `IN` 运算符检查某个值是否等于列表中的任意一个值。
- 使用 `NOT IN` 运算符来判断一个值是否不等于列表中的任何值。