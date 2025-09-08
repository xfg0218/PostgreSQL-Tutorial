**摘要**：在本教程中，您将学习如何使用 `PostgreSQL` 的 `WHERE` 子句来筛选表中的行。

# `PostgreSQL WHERE` 子句

[ `SELECT FROM` ](../第1节-PostgreSQL入门/从表中查询数据.md) 语句从表中所有行的一个或多个列中查询数据。实际上，你经常需要选择满足某个条件的行。

要根据条件从表中筛选行，需在 `SELECT` 语句中使用 `WHERE` 子句:

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

执行该语句时，`PostgreSQL` 会先计算 `FROM` 子句，然后计算 `WHERE` 子句，最后计算  `SELECT` 子句:

- 首先,通过执行 `FROM` 子句检索 `table_name` 中的每一行。
- 其次,评估 `WHERE` 子句中的 `condition` ，如果 `condition` 为真，则将该行包含在结果集中。
- 第三，从选中的行中选择 `SELECT` 子句中指定的列。

如果 `table_name` 中没有行满足 `condition` ，则SELECT语句会返回一个空集，其中不包含任何行。

# `PostgreSQL WHERE` 子句示例

假设我们有如下的 `inventories` 表:

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

以下语句使用 `WHERE` 子句来检索数量大于9的产品:

```sql
SELECT name, quantity
FROM inventories
WHERE quantity > 9;
```

输出:

```sql
       name       | quantity
------------------+----------
 iPhone 14 Pro    |       10
 Galaxy S23 Ultra |       15
```

它的工作原理。

首先，`FROM` 子句从 `inventories` 表中选取所有行:

```sql
       name       |  brand  | quantity |  price
------------------+---------+----------+---------
 iPhone 14 Pro    | Apple   |       10 |  999.99
 Galaxy S23 Ultra | Samsung |       15 | 1199.99
 Pixel 7 Pro      | Google  |        8 |  899.99
 Xperia 1 IV      | Sony    |        7 | 1299.99
```

其次，`WHERE` 子句计算库存表中的每一行，检查数量是否大于 10，并将中间结果集中的行包括在内:

```sql
       name       |  brand  | quantity |  price
------------------+---------+----------+---------
 iPhone 14 Pro    | Apple   |       10 |  999.99
 Galaxy S23 Ultra | Samsung |       15 | 1199.99
```

它返回两行,数量分别为10和15。

第三, `SELECT` 子句从 `name` 和 `quantity` 列中查询数据:

```sql
       name       | quantity
------------------+----------
 iPhone 14 Pro    |       10
 Galaxy S23 Ultra |       15
```

# 比较运算符

除了大于运算符 `>` 之外，你还可以在 `WHERE` 子句中使用其他比较运算符:

| Operator | Meaning |
|:---------|:--------|
|  = | Equal to |
| != (<>) | Not Equal To |
| > | Greater than |
| >= | Greater than or equal to |
| < | Less than |
| <= | Less than or equal to |

# 等于运算符 `(=)`

如果两个值相等，相等运算符返回true。

例如，以下语句在WHERE子句中使用等于运算符 `（=）`来查询 `inventories` 中数量等于10的产品:

```sql
SELECT name, quantity
FROM inventories
WHERE quantity = 10;
```

输出:

```sql
     name      | quantity
---------------+----------
 iPhone 14 Pro |       10
```

该查询返回 `iPhone 14 Pro` ，因为只有该产品的数量为10。


# 不等于运算符`（!=）`

以下语句使用不等运算符 `（!=）`从 `inventories` 表中查询数量不等于10的产品:

```sql
SELECT name, quantity
FROM inventories
WHERE quantity != 10;
```

输出:

```sql
       name       | quantity
------------------+----------
 Galaxy S23 Ultra |       15
 Pixel 7 Pro      |        8
 Xperia 1 IV      |        7
```

请注意，`PostgreSQL` 也使用 `<>` 作为不等于运算符，因此你可以交替使用 `!=` 和 `<>` 运算符。

# `WHERE` 子句和列别名

由于 `PostgreSQL` 先计算 `WHERE` 子句，再计算 `SELECT` 子句，因此在计算 `WHERE` 子句时，列别名不可用。

以下语句尝试在 `WHERE` 中使用 `amount` 列别名，结果导致错误:

```sql
SELECT
  name,
  quantity * price AS amount
FROM
  inventories
WHERE
  amount > 10000;
```

错误:

```sql
column "amount" does not exist
```

输出表明 `amount` 列不存在。要修复此错误，您需要按如下方式在 `WHERE` 子句中使用该表达式:

```sql
SELECT
  name,
  quantity * price AS amount
FROM
  inventories
WHERE
  quantity * price > 10000;
```

输出:

```sql
       name       |  amount
------------------+----------
 Galaxy S23 Ultra | 17999.85
```

# 总结

- 使用 `WHERE` 子句根据条件从表中筛选行。
- `PostgreSQL` 在 `FROM` 子句之后、`SELECT` 子句之前对 `WHERE` 子句进行求值。