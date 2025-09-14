**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `CHAR` 数据类型在数据库中存储固定长度的字符。

以下是定义 `CHAR` 类型列的语法：

```sql
column_name CHAR(n)
```

在这种语法中，`column_name` 将精确存储 `n` 个字符。

如果您存储的字符串长度小于 `n` ，`PostgreSQL` 会用空格填充该字符串以达到所需长度。

例如，如果你有一个 `CHAR(5)` 列，并插入一个包含两个字符的字符串 `（'hi'）`，`PostgreSQL` 会填充空格，因此存储的字符串长度将为5个字符。

如果在 `CHAR` 列中插入或更新的字符串长度超过指定长度，`PostgreSQL` 会发出错误。因此，你可以将 `CHAR` 列可存储的字符数限制作为验证规则，以维护数据完整性。

> 实际上，在存储文本字符串时，你通常会使用 `CHARACTER VARYING（VARCHAR）` 类型而非 `char` 类型。

# `PostgreSQL CHAR` 示例 
首先，创建一个新表，名为 `products`：

```sql
CREATE TABLE products (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR NOT NULL,
  upc CHAR(12) NOT NULL
);
```

`books` 表中的 `upc` （通用产品代码）列的数据类型为 `CHAR(12)` 。它可以存储最多12个字符的字符串。

其次，向 `products` 表中插入一些行： 

```sql
INSERT INTO
  products (name, upc)
VALUES
  ('iPhone 16 Pro', '194252714096'),
  ('iPhone 16 Pro Max', '194252697559') 
RETURNING *;
```

输出：

```sql
 id |       name        |     upc
----+-------------------+--------------
  1 | iPhone 16 Pro     | 194252714096
  2 | iPhone 16 Pro Max | 194252697559
```

第三，插入 `UPC` 少于13个字符的产品：

```sql
INSERT INTO
  products (name, upc)
VALUES
  ('iPhone 17 Pro', '194252714'),
  ('iPhone 17 Pro Max', '194252697') 
RETURNING *;
```

输出：

```sql
 id |       name        |     upc
----+-------------------+--------------
  3 | iPhone 17 Pro     | 194252714
  4 | iPhone 17 Pro Max | 194252697
```

`PostgreSQL` 会用空格填充这些 `UPC` 值。

请注意，如果你使用 `length()` 函数，`PostgreSQL` 会在返回字符数之前自动去除空格：

```sql
SELECT
  upc,
  length (upc)
FROM
  products;
```

输出：

```sql
     upc      | length
--------------+--------
 194252714096 |     12
 194252697559 |     12
 194252714    |      9
 194252697    |      9
```

要查找存储字符的大小，可以使用 `octet_length()` 函数，该函数返回字节数：

```sql
SELECT
  upc,
  octet_length(upc)
FROM
  products;
```

输出：

```sql
     upc      | octet_length
--------------+--------------
 194252714096 |           12
 194252697559 |           12
 194252714    |           12
 194252697    |           12
```

如果你希望 `upc` 列正好包含12个字符，不多也不少，你可以使用 `CHECK` 约束来执行这条规则。

第四，重新创建 `products` 表，使 `UPC` 列具有 `CHECK` 约束：

```sql
DROP TABLE products;

CREATE TABLE products (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR NOT NULL,
  upc CHAR(12) NOT NULL CHECK (length (upc) = 12)
);
```

最后，尝试向 `products` 表中插入一个小于12的值将会导致错误：

```sql
INSERT INTO
  products (name, upc)
VALUES
  ('iPhone 17 Pro Max', '194252697');
```

错误：

```sql
new row for relation "products" violates check constraint "products_upc_check"
```

# 总结

- `CHAR` 或 `CHARACTER` 是一种带填充空格的固定长度字符串类型。
