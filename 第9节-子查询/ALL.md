**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `ALL` 运算符来将一个值与子查询返回的一组值中的所有值进行比较。

# `PostgreSQL ALL` 运算符简介

在 `PostgreSQL` 中，`ALL` 运算符允许你将一个值与子查询返回的一组值进行比较。

`ALL` 运算符的语法如下：

```sql
value operator ALL(subquery)
```

在以下语法中：

- `value`：要比较的列或表达式。
- `operator`：比较运算符（如 `=`、`<`、`>`、`<=`、`>=`、`!=`）。
- `subquery`：返回单列值的子查询。

如果比较结果对于集合中的所有值都为 `true`，则 `ALL` 运算符返回 `true`。如果有任何一个比较结果为 `false`，则返回 `false`。

如果子查询没有返回任何行，`ALL` 运算符总是返回 `true`。

# `PostgreSQL ALL` 运算符示例

假设有一个 `products` 表，包含 `id`、`name`、`price` 和 `brand` 字段。

创建表并插入数据的 `SQL` 如下：

```sql
CREATE TABLE products (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(50) NOT NULL,
  price DEC(11, 2) NOT NULL CHECK (price > 0),
  brand VARCHAR(50) NOT NULL
);

INSERT INTO products (name, price, brand) VALUES
  ('Galaxy S24', 799.99, 'Samsung'),
  ('iPhone 16', 1099.99, 'Apple'),
  ('iPhone 16 Pro Max', 1399.99, 'Apple'),
  ('iPhone 16 Plus', 1199.99, 'Apple'),
  ('Galaxy S24 Ultra', 1299.99, 'Samsung'),
  ('Galaxy S24 Plus', 1119.99, 'Samsung');
```

表数据如下：

| id | name  | price    | brand   |
|:----:|:----:|:----:|:----:|
| 1  | Galaxy S24          | 799.99   | Samsung |
| 2  | iPhone 16           | 1099.99  | Apple   |
| 3  | iPhone 16 Pro Max   | 1399.99  | Apple   |
| 4  | iPhone 16 Plus      | 1199.99  | Apple   |
| 5  | Galaxy S24 Ultra    | 1299.99  | Samsung |
| 6  | Galaxy S24 Plus     | 1119.99  | Samsung |


以下语句使用 `ALL` 运算符查找所有比所有 `Samsung` 产品都贵的产品：

```sql
SELECT
  name,
  brand,
  price
FROM
  products
WHERE
  price > ALL (
    SELECT
      price
    FROM
      products
    WHERE
      brand = 'Samsung'
  );
```

输出：

```sql
       name        | brand |  price
-------------------+-------+---------
 iPhone 16 Pro Max | Apple | 1399.99
```

查询的工作原理。

子查询会筛选出所有 `Samsung` 品牌产品的价格：

```sql
SELECT
  price
FROM
  products
WHERE
  brand = 'Samsung'
```

它返回三行：

```sql
  price
---------
  799.99
 1299.99
 1119.99
```

`ALL` 运算符会将外部查询中每个产品的价格与这些价格进行比较。

外部查询中的 `WHERE` 子句会检查每个产品的价格是否高于所有 `Samsung` 产品的价格。

输出显示，只有 `iPhone 16 Pro Max` 的价格（ `1399.99` ）高于所有 `Samsung` 产品的价格。

你可以使用 `MAX` 聚合函数重写上述查询：

```sql
SELECT
  name,
  brand,
  price
FROM
  products
WHERE
  price > (
    SELECT
      MAX(price)
    FROM
      products
    WHERE
      brand = 'Samsung'
  );
```

输出：

```sql
       name        | brand |  price
-------------------+-------+---------
 iPhone 16 Pro Max | Apple | 1399.99
```

它的工作原理。

子查询返回所有 `Samsung` 产品的最高价格：

```sql
SELECT
  MAX(price)
FROM
  products
WHERE
  brand = 'Samsung'
```

输出：

```sql
   max
---------
 1299.99
```

外部查询将每个产品的价格与所有 `Samsung` 产品的最高价格进行比较，并选择那些比最贵的 `Samsung` 产品还要贵的产品。

# 总结

- 使用 `PostgreSQL` 的 `ALL` 运算符将一个值与子查询返回的所有值进行比较。

