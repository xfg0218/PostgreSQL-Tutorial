**摘要**: 在本教程中，你将学习如何使用 `PostgreSQL` 的 `CHECK` 约束来确保表列中的值满足某个条件。

# `PostgreSQL CHECK` 约束简介

`PostgreSQL` 的 `CHECK` 约束通过确保列中的值必须满足布尔表达式来维护数据完整性。

当列具有 `CHECK` 约束时，如果您尝试插入或更新导致布尔表达式为 `false` 的值，`PostgreSQL` 会发出约束冲突并拒绝这些更改。

`CHECK` 约束有助于在数据库级别实施数据完整性规则。它们可以防止向表列中插入或更新无效数据。

例如，你可以使用 `CHECK` 约束来防止向 `price` 列或 `products` 表中插入负值。

以下是 `CHECK` 约束的语法：

```sql
CREATE TABLE table_name (
    column1 data_type CONSTRAINT constraint_name CHECK(boolean_expression),
    ...

);
```

在此语法中:

- 首先，向表列添加一个 `CHECK` 约束。
- 其次，在 `CONSTRAINT` 关键字后提供一个约束名称。如果发生约束违反错误，该约束名称将在错误信息中显示。如果不提供约束名称，`PostgreSQL` 会为该约束隐式生成一个名称。
- 第三，在 `CHECK` 关键字后面的括号`()`中放入一个布尔表达式，用于检查要插入或更新到列中的值。该布尔表达式可以引用同一张表的列，而不能引用其他表的列。

以下是不使用约束名称的更简洁语法：

```sql
CREATE TABLE table_name (
    column1 data_type CHECK(boolean_expression),
    ...
);
```

这个 `CHECK` 约束是列约束，因为它附加到特定的列 `(column1)` 。

值得注意的是，您可以对同一列应用多个约束。例如，您可以对同一列同时使用 `NOT NULL` 和 `CHECK` 约束：

```sql
CREATE TABLE table_name (
    column1 data_type NOT NULL CHECK(boolean_expression),
    ...
);
```

或者，您可以将 `CHECK` 约束定义为表约束：

```sql
CREATE TABLE table_name (
    column1 data_type NOT NULL,
    ...,
   CHECK(boolean_expression)
);
```

在这种语法中，您要在列列表之后定义 `CHECK` 约束。当在布尔表达式中引用多个表列时，这种语法很方便。

# 基本的 `PostgreSQL CHECK` 约束示例

首先，[创建一个表](../第1节-PostgreSQL入门/创建新表.md)，名为 `products` ，并添加一个 `CHECK` 约束，以确保价格大于或等于0：

```sql
CREATE TABLE products (
  product_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  price DEC(11, 2) NOT NULL CONSTRAINT positive_price CHECK (price >= 0),
  discounted_price DEC(11, 2) NOT NULL DEFAULT 0
);
```

接下来，尝试向 `products` 表中插入一行包含负价格的数据：

```sql
INSERT INTO
  products (name, price)
VALUES
  ('iPhone Pro 15', -1299.99);
```

`PostgreSQL` 发出了以下错误：

```sql
new row for relation "products" violates check constraint "positive_price"
```

然后，插入一行包含有效价格的新记录：

```sql
INSERT INTO
  products (name, price)
VALUES
  ('iPhone Pro 15', 1299.99) RETURNING *;
```

输出：

```sql
 product_id |     name      |  price  | discounted_price
------------+---------------+---------+------------------
          2 | iPhone Pro 15 | 1299.99 |             0.00
```

之后，将价格更新为无效值：

```sql
UPDATE products
SET
  price = -899.99
WHERE
  product_id = 2;
```

`PostgreSQL` 发出了相同的检查约束冲突：

```sql
new row for relation "products" violates check constraint "positive_price"
```

最后，将价格更新为有效的价格：

```sql
UPDATE products
SET
  price = 999.99
WHERE
  product_id = 2 
RETURNING *;
```

输出：

```sql
 product_id |     name      | price  | discounted_price
------------+---------------+--------+------------------
          2 | iPhone Pro 15 | 999.99 |             0.00
```

输出表明该语句已成功更新产品价格。

# 为表添加 `CHECK` 约束

您可以使用 `ALTER TABLE ... ADD CONSTRAINT` 语句向现有表添加 `CHECK` 约束。

以下是该语句的语法：

```sql
ALTER TABLE table_name 
ADD CONSTRAINT constraint_name 
CHECK (boolean_expression)
```

例如，以下语句添加了一个 `CHECK` 约束，用于检查折扣价是否低于原价：

```sql
ALTER TABLE products
ADD CONSTRAINT discounted_price_check
CHECK (discounted_price < price AND discounted_price > 0);
```

`PostgreSQL` 会发出错误，因为表中的某些行违反了新的 `CHECK` 约束：

```sql
check constraint "discounted_price_check" of relation "products" is violated by some row
```

原因是行 `ID 2` 的折扣价为 `0` ，这违反了新的 `CHECK` 约束。

要解决此问题，你可以更新现有行的折扣价格，使其符合新的 `CHECK` 约束：

```sql
UPDATE products
SET discounted_price = price * 0.9
RETURNING *;
```

输出：

```sql
 product_id |     name      | price  | discounted_price
------------+---------------+--------+------------------
          2 | iPhone Pro 15 | 999.99 |           899.99
```

然后再次向 `products` 表添加 `CHECK` 约束：

```sql
ALTER TABLE products
ADD CONSTRAINT discounted_price_check
CHECK (discounted_price < price AND discounted_price > 0);
```

如果您尝试插入一行数据，其中折扣价高于或等于原价，`PostgreSQL` 会发出检查违规提示。例如：

```sql
INSERT INTO products(name, price, discounted_price)
VALUES('iPhone Pro 15', 1299.99, 1399.99);
```

错误：

```sql
ERROR: new row for relation "products" violates check constraint "discounted_price_check"
```

# 从表中删除 `CHECK` 约束

要从表中删除 `CHECK` 约束，需使用 `ALTER TABLE ... DROP CONSTRAINT` 语句：

```sql
ALTER TABLE table_name
DROP CONSTRAINT IF EXISTS constraint_name;
```

在此语法中：

- 首先，指定你要从中移除 `CHECK` 约束的表名。
- 其次，在 `DROP CONSTRAINT` 子句中提供要删除的约束名称。
- 第三，使用可选的 `IF EXISTS` 选项，以防止因尝试删除不存在的 `CHECK` 约束而出现错误。

例如，以下语句会从 `products` 表中删除 `discounted_price` 的 `CHECK` 约束：

```sql
ALTER TABLE products
DROP CONSTRAINT IF EXISTS discounted_price_check;
```

# 结论

- `CHECK` 约束向表中的一个或多个列添加验证逻辑，以确保数据完整性。
- 使用 `CONSTRAINT ... CHECK` 为表定义一个 `CHECK` 约束。
- 使用 `ALTER TABLE ... ADD CONSTRAINT` 语句向表中添加 `CHECK` 约束。
- 使用 `ALTER TABLE ... DROP CONSTRAINT` 语句从表中删除 `CHECK` 约束。