**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `boolean` 类型来存储布尔值，包括 `true` 和 `false` 。

# 开始使用 `PostgreSQL` 布尔类型

在 `PostgreSQL` 中，你可以使用 `boolean` 类型来存储布尔数据，包括 `true` 和 `false` 。

`boolean` 类型有三个值：

- `true`
- `false`
- `NULL`

`NULL` 表示未知状态。

`PostgreSQL` 使用 `boolean` 关键字来表示 `boolean` 类型。你也可以使用 `bool` ，它是 `boolean` 类型的缩写。

`PostgreSQL` 使用 `1` 字节来存储 `boolean` 值。

除了 `true` 和 `false` 之外，`PostgreSQL` 在 `SQL` 查询中还使用多种表示方式来表示 `true` 和`false`：

| True | False |
|:----:|:-----:|
| true | false |
| 't' | 'f' |
| 'true' | 'false' |
| 'y' | 'n' |
| 'yes' | 'no' |
| '1' | '0' |

> 请注意，除了 `true` 和 `false` 之外，所有其他值都需要用单引号括起来。

# `PostgreSQL` 布尔类型示例

首先，创建一个表，名为 `products` ，用于存储产品数据：

```sql
CREATE TABLE products (
  id INT GENERATED ALWAYS AS IDENTITY,
  name VARCHAR NOT NULL,
  is_active BOOL
);
```

`products` 表包含 `is_active` 列，其类型为 `boolean` ，因此可以存储 `true` 、`false` 或 `NULL` 。

> 如果你不想在布尔值列中存储 `NULL` ，可以添加 `NOT NULL` 约束。

其次，向 `products` 表中插入一些行：

```sql
INSERT INTO
  products (name, is_active)
VALUES
  ('iPhone 6', false),
  ('iPhone 16', true),
  ('Galaxy M01', 'f'),
  ('Galaxy Z Fold6', 't'),
  ('Galaxy Z Flip6', NULL) 
RETURNING *;
```

输出：

```sql
 id |      name      | is_active
----+----------------+-----------
  1 | iPhone 6       | f
  2 | iPhone 16      | t
  3 | Galaxy M01     | f
  4 | Galaxy Z Fold6 | t
  5 | Galaxy Z Flip6 | NULL
```

第三，检索非活跃产品：

```sql
SELECT * FROM products
WHERE is_active = false;
```

输出：

```sql
 id |    name    | is_active
----+------------+-----------
  1 | iPhone 6   | f
  3 | Galaxy M01 | f
```

你可以使用其他假值，如 `'f'`、`'0'`、 `'n'` 或 `'no'`。例如：

```sql
SELECT * FROM products
WHERE is_active = 'f';
```

第四，从 `products` 表中查询活跃产品：

```sql
SELECT * FROM products
WHERE is_active = true;
```

输出：

```sql
 id |      name      | is_active
----+----------------+-----------
  2 | iPhone 16      | t
  4 | Galaxy Z Fold6 | t
```

# 与布尔值的隐式比较

`PostgreSQL` 允许你仅使用列名来隐式地将其与 `true` 进行比较。例如，以下语句用于查询活跃产品：

```sql
SELECT * FROM products
WHERE is_active;
```

输出：

```sql
 id |      name      | is_active
----+----------------+-----------
  2 | iPhone 16      | t
  4 | Galaxy Z Fold6 | t
```

要否定一列的值，可以使用 `NOT` 运算符：

```sql
SELECT * FROM products
WHERE NOT is_active;
```

输出：

```sql
 id |    name    | is_active
----+------------+-----------
  1 | iPhone 6   | f
  3 | Galaxy M01 | f
```

# 为布尔列设置默认值

您可以使用 `DEFAULT` 约束为 `boolean` 列设置默认值：

```sql
column_name BOOL DEFAULT boolean_value;
```

例如，以下语句创建了 `users` 表，其中 `email_confirmed` 列的默认值设置为 `false`：

```sql
CREATE TABLE users (
  id INT GENERATED ALWAYS AS IDENTITY,
  email VARCHAR NOT NULL,
  password VARCHAR NOT NULL,
  email_confirmed BOOL NOT NULL DEFAULT false
);
```

# 总结

- 使用 `PostgreSQL` 的 `boolean` 类型存储布尔值。
- `PostgreSQL` 对布尔值使用隐式比较。