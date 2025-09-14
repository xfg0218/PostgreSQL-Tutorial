**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `VARCHAR` 类型在数据库中存储可变长度的字符串。

# `PostgreSQL VARCHAR` 类型入门

`CHARACTER VARYING` 类型（即 `VARCHAR` ）允许您在数据库中存储可变长度的字符串。

以下是定义 `CHARACTER VARYING` 类型列的语法：

```sql
column_name CHARACTER VARYING (n)
```

实际上，你经常会使用 `VARCHAR` ，它是 `CHARACTER VARYING` 类型的同义词：

```sql
column_name VARCHAR(n)
```

在这种语法中，需要指定长度说明符（ `n` ），它是 `VARCHAR` 列可存储的最大字符数。`n` 是一个正整数，且不能超过 `10,485,760` 。

如果您尝试向该列中插入或更新超过 `n` 个字符的字符串，`PostgreSQL` 将会报错。

限制存储在数据库中的字符串长度主要有两个原因：

- 首先，你需要在数据库层面创建一个验证规则，以确保字符串长度不超过特定长度。
- 其次，你希望应用的用户界面因一个非常长的字符串而出现故障。

与 `CHAR`  列不同，如果存储在 `VARCHAR` 列中的值长度小于 `n` ，`PostgreSQL` 不会填充空格。

如果您想存储任意长度的字符串，可以在列定义中省略长度说明符：

```sql
column_name VARCHAR
```

在这种情况下，`column_name` 可以存储任意长度的字符串。

# `PostgreSQL VARCHAR` 类型示例

首先，创建一个 `products` 表：

```sql
CREATE TABLE products (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(50) NOT NULL,
  description VARCHAR
);
```

`products` 表有两个 `VARCHAR` 列：

- `name` 列最多可存储50个字符。如果尝试插入或更新长度超过50的字符串，`PostgreSQL` 会发出错误。
- `description` 列可以存储任意长度的字符串。

"name"列有长度限制，以确保产品名称能在收据上清晰显示。没有此限制，产品名称可能会与其他信息重叠。

其次，向 `products` 表中插入一些行：

```sql
INSERT INTO
  products (name, description)
VALUES
  (
    'Galaxy S21',
    'Introducing a high-end smartphone with a 6.2-inch screen, triple camera, and 5G connectivity.'
  ),
  (
    'iPhone 16',
    'Introducing the Apple latest model featuring a 6.1-inch Super Retina XDR display and A18 Bionic chip.'
  ) RETURNING *;
```

输出：

```sql
 id |    name    |                                              description
----+------------+-------------------------------------------------------------------------------------------------------
  1 | Galaxy S21 | Introducing a high-end smartphone with a 6.2-inch screen, triple camera, and 5G connectivity.
  2 | iPhone 16  | Introducing the Apple latest model featuring a 6.1-inch Super Retina XDR display and A18 Bionic chip.
```

第三，尝试插入一个名称超过50个字符的新产品。

```sql
INSERT INTO products(name)
VALUES( 'Ultra-High Definition Quantum Dot Curved Smart TV 90-Inch');
```

`PostgreSQL` 发出了以下错误：

```sql
value too long for type character varying(50)
```

# 总结

- 使用 `VARCHAR` 类型在数据库中存储可变长度的字符串。
- 使用不带长度说明符的 `VARCHAR` 来存储任意长度的字符串。