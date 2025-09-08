**摘要**: 在本教程中，你将学习如何使用 `PostgreSQL` 的 `DELETE` 语句从表中删除一行或多行。

# `PostgreSQL DELETE` 语句

`PostgreSQL` 的 `DELETE` 语句是一个强大的工具，它能从表中永久删除一行或多行数据。

以下是 `DELETE` 语句的语法:

```sql
DELETE FROM table_name
WHERE condition;
```

在此语法中:

- 首先,在 `DELETE FROM` 子句中指定你想要删除行的表名。
- 其次,在 `WHERE` 子句中提供一个条件，以筛选要删除的行。

由于 `WHERE` 子句是可选的，因此你可以在 `DELETE` 语句中省略它。在这种情况下，`DELETE` 语句将删除表中的所有行。

如果没有行满足 `WHERE` 子句中的条件，`DELETE` 语句将不会从表中删除任何行。

`PostgreSQL` 会返回一个命令标签，该标签指示由 `DELETE` 语句删除的行数:

```sql
DELETE count;
```

如果计数为零，`DELETE` 语句不会从表中删除任何行。

# 创建示例表

我们将从 `inventories` 表中删除行:

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

# 从表中删除一行

以下语句使用 `DELETE` 语句从 `inventories` 表中删除一行:

```sql
DELETE FROM inventories
WHERE name = 'iPhone 14 Pro';
```

输出:

```sql
DELETE 1
```

在这个示例中，`WHERE` 子句中的条件匹配了一行。因此，`DELETE` 语句只删除了该行。

输出表明，`DELETE` 语句成功删除了一行。

# 从表中删除所有行

以下语句使用 `DELETE` 语句从 `inventories` 表中删除所有行。

```sql
DELETE FROM inventories;
```

这条 `DELETE` 语句没有使用 `WHERE` 子句，因此它会删除 `inventories` 表中所有剩余的行。当你想要彻底清空一张表时（例如，在重新填充新数据之前），这可能会很有用。

输出:

```sql
DELETE 3
```

输出显示，`DELETE` 语句从 `inventories` 表中删除了三行。

# 返回已删除的行

`DELETE` 语句有一个可选的 `RETURNING` 子句，用于返回被删除的行。

```sql
DELETE FROM table_name
WHERE condition
RETURNING column1, column2, ...;
```

在该语法中,请指定要返回的已删除行中用逗号分隔的列列表。如果要返回已删除行的所有列，可以按如下方式使用星号 `(*)`:

```sql
DELETE FROM table_name
WHERE condition
RETURNING *;
```

例如,首先,向 `inventories` 表中[插入新行](../第1节-PostgreSQL入门/向表中插入数据.md):

```sql
INSERT INTO inventories (name, brand, quantity, price)
VALUES ('iPhone 15 Pro', 'Apple', 5, 1299.99);
```

其次，删除 `iPhone 15 Pro` 并返回已删除的行:

```sql
DELETE FROM inventories
WHERE name = 'iPhone 15 Pro'
RETURNING *;
```

输出:

```sql
     name      | brand | quantity |  price
---------------+-------+----------+---------
 iPhone 15 Pro | Apple |        5 | 1299.99
```

# 总结

- 使用 `PostgreSQL` 的 `DELETE` 语句从表中删除一行或多行。
- 使用 `RETURNING` 子句将删除的行返回给客户端。
