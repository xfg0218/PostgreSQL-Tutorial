**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `TEXT` 数据类型来存储可变长度的字符数据。

# `PostgreSQL TEXT` 数据类型入门

`PostgreSQL` 的 `TEXT` 数据类型是一种可变长度的字符数据类型。`TEXT` 数据类型能够处理大量文本，非常适合存储描述、文档和评论。

以下是定义具有 `TEXT` 数据类型的表列的语法：

```sql
CREATE TABLE table_name {
    column_name TEXT,
    ...
}
```

`TEXT` 数据类型列的主要特点如下：

- 可变长度：`TEXT` 列可以存储任意长度的字符串，最大为1GB。1GB是 `PostgreSQL` 中的字段长度限制，不仅限于 `TEXT` 字段。
- 尾随空格：`TEXT` 列会保留尾随空格，且在检索时不会对其进行修剪。这些尾随空格在进行比较时十分重要。
- 性能：`TEXT` 类型与 `VARCHAR` 类型的性能相同。

在实际场景中，`TEXT` 数据类型更适合存储无长度限制的可变长度字符数据，使您能够处理任何大小的字符数据。

> 请注意，不带长度说明符的 `VARCHAR` 的行为与 `TEXT` 类似。

# `PostgreSQL TEXT` 数据类型示例

首先，创建一个名为 `products` 的表来存储产品信息：

```sql
CREATE TABLE products (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(50) NOT NULL,
  description TEXT
);
```

在这个 `products` 表中，`description` 列的数据类型为 `TEXT`。

其次，将一个产品插入到 `products` 表中：

```sql
INSERT INTO
  products (name, description)
VALUES
  (
    'iPhone 17',
    'Introducing a new AI smartphone a 6.2-inch screen, triple camera, and 5G connectivity.'
  ) RETURNING *;
```

输出：

```sql
 id |   name    |                                      description
----+-----------+----------------------------------------------------------------------------------------
  1 | iPhone 17 | Introducing a new AI smartphone a 6.2-inch screen, triple camera, and 5G connectivity.
```

# 总结

- 使用 `PostgreSQL` 的 `TEXT` 数据类型存储可变长度的字符数据。
