**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 的 `NATURAL JOIN` 基于具有相同名称的列来连接表。

# 深入了解 `PostgreSQL` `NATURAL JOIN` 连接

在 `PostgreSQL` 中，`NATURAL JOIN` 允许你基于两个表中名称相同的列来连接表。

```sql
SELECT select_list
FROM table1
NATURAL [INNER, LEFT, RIGHT, FULL] JOIN table2;
```

在该语法中：

- `NATURAL JOIN` 用于连接两个表，即 `table1` 和 `table2`。这些表需要有相同的列名以及可兼容的匹配类型。
- `NATURAL JOIN` 可以是 `INNER JOIN`、`LEFT JOIN`、`RIGHT JOIN` 和 `FULL JOIN`。
- 如果你在 `NATURAL` 关键字后没有指定连接类型，`PostgreSQL` 会默认使用 `INNER JOIN` 。如果 `table1` 和 `table2` 没有共同的列，`NATURAL JOIN` 的行为就像 `CROSS JOIN`。

# 设置示例表

首先，创建一个名为 `brands` 的新表：

```sql
CREATE TABLE brands (
  brand_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  brand_name VARCHAR(255) NOT NULL
);
```

其次，向 `brands` 表中插入行：

```sql
INSERT INTO
  brands (brand_name)
VALUES
  ('Apple'),
  ('Samsung'),
  ('Google')
RETURNING *;
```

输出：

```sql
brand_id | brand_name
----------+------------
        1 | Apple
        2 | Samsung
        3 | Google
```

第三，创建一个名为 `products` 的新表：

```sql
CREATE TABLE products (
  product_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  product_name VARCHAR(100) NOT NULL,
  price DECIMAL(10, 2) NOT NULL,
  brand_id INT,
  FOREIGN KEY (brand_id) REFERENCES brands (brand_id)
);
```

最后，向 `products` 表中插入行：

```sql
INSERT INTO
  products (product_name, price, brand_id)
VALUES
  ('iPhone 14 Pro', 999.99, 1),
  ('iPhone 15 Pro', 1199.99, 1),
  ('Galaxy S23 Ultra', 1149.47, 2),
  ('Oppo Find Flip', 499.99, NULL)
RETURNING *;
```

输出：

```sql
 product_id |   product_name   |  price  | brand_id
------------+------------------+---------+----------
          1 | iPhone 14 Pro    |  999.99 |        1
          2 | iPhone 15 Pro    | 1199.99 |        1
          3 | Galaxy S23 Ultra | 1149.47 |        2
          4 | Oppo Find Flip   |  499.99 |     NULL
```

# `PostgreSQL NATURAL JOIN` 连接示例

我们将使用 `NATURAL JOIN` 来连接 `brands` 和 `products` 表。

## `INNER JOIN`

以下语句使用 `NATURAL JOIN` 在 `products` 表和 `brands` 表之间执行 `INNER JOIN`：

```sql
SELECT
  product_name,
  price,
  brand_name
FROM
  products
  NATURAL INNER JOIN brands;
```

输出：

```sql
   product_name   |  price  | brand_name
------------------+---------+------------
 iPhone 14 Pro    |  999.99 | Apple
 iPhone 15 Pro    | 1199.99 | Apple
 Galaxy S23 Ultra | 1149.47 | Samsung
```

## LEFT JOIN

以下示例使用 `NATURAL JOIN` 通过 `LEFT JOIN` 来连接产品表和品牌表：

```sql
SELECT
  product_name,
  price,
  brand_name
FROM
  products
  NATURAL LEFT JOIN brands;
```

输出：

```sql
   product_name   |  price  | brand_name
------------------+---------+------------
 iPhone 14 Pro    |  999.99 | Apple
 iPhone 15 Pro    | 1199.99 | Apple
 Galaxy S23 Ultra | 1149.47 | Samsung
 Oppo Find Flip   |  499.99 | NULL
```

## RIGHT JOIN

以下语句使用 `NATURAL JOIN` 通过 `RIGHT JOIN` 来连接 `products` 表和 `brands` 表：

```sql
SELECT
  product_name,
  price,
  brand_name
FROM
  products
  NATURAL RIGHT JOIN brands;
```

输出：

```sql
   product_name   |  price  | brand_name
------------------+---------+------------
 iPhone 14 Pro    |  999.99 | Apple
 iPhone 15 Pro    | 1199.99 | Apple
 Galaxy S23 Ultra | 1149.47 | Samsung
 NULL             |    NULL | Google
```

## FULL JOIN

以下示例使用 `NATURAL JOIN` 通过 `FULL JOIN` 来连接 `products` 表和 `brands` 表

```sql
SELECT
  product_name,
  price,
  brand_name
FROM
  products
  NATURAL FULL JOIN brands;
```

输出：

```sql
   product_name   |  price  | brand_name
------------------+---------+------------
 iPhone 14 Pro    |  999.99 | Apple
 iPhone 15 Pro    | 1199.99 | Apple
 Galaxy S23 Ultra | 1149.47 | Samsung
 Oppo Find Flip   |  499.99 | NULL
 NULL             |    NULL | Google
```

# `PostgreSQL NATURAL JOIN` 注意事项

`PostgreSQL` 的 `NATURAL JOIN` 依赖相同的列名来匹配行。如果更改表结构，查询的运行结果可能会与预期不同。例如：

```sql
DROP TABLE products, brands;

CREATE TABLE brands (
  brand_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(255) NOT NULL
);

CREATE TABLE products (
  product_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  price DECIMAL(10, 2) NOT NULL,
  brand_id INT,
  FOREIGN KEY (brand_id) REFERENCES brands (brand_id)
);

INSERT INTO
  brands (name)
VALUES
  ('Apple'),
  ('Samsung'),
  ('Google') 
RETURNING *;

INSERT INTO
  products (name, price, brand_id)
VALUES
  ('iPhone 14 Pro', 999.99, 1),
  ('iPhone 15 Pro', 1199.99, 1),
  ('Galaxy S23 Ultra', 1149.47, 2),
  ('Oppo Find Flip', 499.99, NULL) 
RETURNING *;
```

我们期望以下查询使用 `brand_id` 列来连接这两个表：

```sql
SELECT
  p.name product_name,
  p.price,
  b.name brand_name
FROM
  products p
  NATURAL INNER JOIN brands b;
```

然而，它返回一个空集：

```sql
 product_name | price | brand_name
--------------+-------+------------
```

原因是 `products` 表和 `brands` 表具有相同的 `brand_id` 和 `name` 列。

在这种情况下，`NATURAL JOIN` 会使用 `brand_id` 和 `name` 两列的值来匹配行。`NATURAL JOIN` 的行为类似于以下查询：

```sql
SELECT
  p.name product_name,
  p.price,
  b.name brand_name
FROM
  products p
  INNER JOIN brands b ON b.brand_id = p.brand_id
  AND b.name = p.name;
```

由于产品名称与品牌名称不同，连接条件为假，这导致查询返回空集。

# 总结

- `PostgreSQL` 的 `NATURAL JOIN` 使用共同的列名，基于共同列来连接两个表。
- 谨慎使用 `NATURAL JOIN` 。