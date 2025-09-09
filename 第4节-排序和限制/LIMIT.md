**摘要**： 在本教程中，你将学习如何使用 `PostgreSQL` 的 `LIMIT` 子句来仅检索行的一个子集。

# `PostgreSQL LIMIT` 入门

`LIMIT` 子句允许您从 `SELECT` 语句中仅检索行的一个子集。在处理大型结果集时，它会很方便。

以下是 `LIMIT` 的语法：

```sql
SELECT
  column1,
  column2
FROM
  table_name
ORDER BY
  sort_expression
LIMIT
  row_count;
```

在以下语法中：

- 首先，在 `FROM` 子句中指定你想要查询数据的表名。
- 其次，在 `SELECT` 子句中提供要返回的列的列表。
- 第三，使用 `ORDER BY` 子句对行进行排序。
- 最后，在 `LIMIT` 子句中指定要返回的行数 `(row_count)` 。

PostgreSQL按以下顺序计算子句：

1. `FROM`
2. `WHERE`
3. `GROUP BY`
4. `LIMIT`

为了获得预期的结果集，务必始终将 `LIMIT` 子句与 `ORDER BY` 子句一起使用。

如果在使用 `ORDER BY` 子句时不搭配 `LIMIT` 子句，`SELECT` 语句返回的行顺序是不确定的，而 `LIMIT` 子句会从未排序的行中选取前几行，这可能会导致意外结果。

# 创建示例表

我们将 [创建一个新表](../第1节-PostgreSQL入门/创建新表.md)，名为 `products` ，该表将存储产品信息，包括产品名称和价格。`products` 表将包含一些示例数据，用于练习 `LIMIT` 子句：

用于创建 `products` 表并填充数据的 `SQL` 脚本：

```sql
CREATE TABLE products (
  product_id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  price DECIMAL(10, 2)
);

INSERT INTO
  products (name, price)
VALUES
  ('iPhone 16 Pro Max', 1649.00),
  ('iPhone 16', 829.00),
  ('Galaxy S24 Ultra', 1299.99),
  ('Galaxy S24 FE', 949.99),
  ('Pixel 9 Pro', 799.00),
  ('Pixel 8a', 399.00),
  ('OnePlus 12', 745.00),
  ('OnePlus Open', 1514.99),
  ('Galaxy Z Fold 6', 2019.99);
```

| product_id | name | price |
|:----:|:----:|:----:|
| 1 | iPhone 16 Pro Max | 1649.00 |
| 2 | iPhone 16 | 829.00 |
| 3 | Galaxy S24 Ultra | 1299.99 |
| 4 | Galaxy S24 FE | 949.99 |
| 5 | Pixel 9 Pro | 799.00 |
| 6 | Pixel 8a | 399.00 |
| 7 | OnePlus 12 | 745.00 |
| 8 | OnePlus Open | 1514.99 |
| 9 | Galaxy Z Fold 6 | 2019.99 |

# `PostgreSQL LIMIT` 示例

以下语句使用 `LIMIT` 从 `products` 表中返回价格最高的五种产品

```sql
SELECT
  name,
  price
FROM
  products
ORDER BY
  price DESC
LIMIT 5;
```

输出：

```sql
       name        |  price
-------------------+---------
 Galaxy Z Fold 6   | 2019.99
 iPhone 16 Pro Max | 1649.00
 OnePlus Open      | 1514.99
 Galaxy S24 Ultra  | 1299.99
 Galaxy S24 FE     |  949.99
```

它的工作原理。

首先，`ORDER BY` 子句按价格从高到低对 `FROM` 子句返回的行进行排序：

| product_id | name | price |
|:----:|:----:|:----:|
| 9 | Galaxy Z Fold 6 | 2019.99 |
| 1 | iPhone 16 Pro Max | 1649.00 |
| 8 | OnePlus Open | 1514.99 |
| 3 | Galaxy S24 Ultra | 1299.99 |
| 4 | Galaxy S24 FE  | 949.99 |
| 2 | iPhone 16 | 829.00 |
| 5 | Pixel 9 Pro | 	799.00 |
| 7 | OnePlus 12 | 745.00 |
| 6 | Pixel 8a | 399.00 |

其次，`SELECT` 子句从 `name` 和 `price` 列中查询数据：

| name | price |
|:----:|:----:|
| Galaxy Z Fold 6 | 2019.99 |
| iPhone 16 Pro Max | 1649.00 |
| OnePlus Open | 1514.99 |
| Galaxy S24 Ultra | 1299.99 |
| Galaxy S24 FE | 949.99 |
| iPhone 16 | 829.00 |
| Pixel 9 Pro | 799.00 |
| OnePlus 12 | 745.00 |
| Pixel 8a | 399.00 |

第三，`LIMIT` 子句会选取前五行并返回它们：

| name | price |
|:----:|:----:|
| Galaxy Z Fold 6 | 2019.99 |
| iPhone 16 Pro Max | 1649.00 |
| OnePlus Open | 1514.99 |
| Galaxy S24 Ultra | 1299.99 |
| Galaxy S24 FE | 949.99 |

# `PostgreSQL LIMIT OFFSET` 子句

要在返回部分行之前跳过某些行，可以使用 `OFFSET` 子句：

```sql
SELECT
  column1,
  column2
FROM
  table_name
ORDER BY
  sort_expression
LIMIT
  row_count
OFFSET
  skip_count;
```

如果 `skip_count` 为零或空值，该语句的作用与不带 `OFFSET` 子句的语句相同。

`OFFSET` 子句在 `LIMIT` 子句返回行的子集之前会跳过若干行 `(skip_count)` 。

以下示例使用 `LIMIT OFFSET` 从 `products` 表中跳过两行，然后返回接下来的五行：

```sql
SELECT
  name,
  price
FROM
  products
ORDER BY
  price DESC
LIMIT 5 OFFSET 2;
```

输出:

```sql
       name       |  price
------------------+---------
 OnePlus Open     | 1514.99
 Galaxy S24 Ultra | 1299.99
 Galaxy S24 FE    |  949.99
 iPhone 16        |  829.00
 Pixel 9 Pro      |  799.00
```

其工作原理。

首先，`FROM` 子句从 `products` 表返回行。

其次，`SELECT` 子句从 `name` 和 `price` 列中查询数据。

第三，`ORDER BY` 子句按价格从高到低对 `FROM` 子句返回的行进行排序。

| name | price |
|:----:|:-----:|
| Galaxy Z Fold 6 | 2019.99 |
| iPhone 16 Pro Max | 1649.00 |
| OnePlus Open | 1514.99 |
| Galaxy S24 Ultra | 1299.99 |
| Galaxy S24 FE | 949.99 | 
| iPhone 16 | 829.00 |
| Pixel 9 Pro | 799.00 |
| OnePlus 12 | 745.00 |
| Pixel 8a | 399.00 |

最后，`OFFSET` 子句跳过两行，`LIMIT` 子句返回接下来的五行：

| LIMIT/OFFSET | product_id | name | price |
|:----:|:----:|:----:|:----:|
| Skipped | 9 | Galaxy Z Fold 6 | 2019.99 |
| Skipped | 1 | iPhone 16 Pro Max | 1649.00 |
| Included | 8 | OnePlus Open | 1514.99 |
| Included | 3 | Galaxy S24 Ultra | 1299.99 |
| Included | 4 | Galaxy S24 FE | 949.99 |
| Included | 2 | iPhone 16 | 829.00 |
| Included | 5 | Pixel 9 Pro | 799.00 |
|          | 7 | OnePlus 12 | 745.00 |
|          | 6 | Pixel 8a | 399.00 |

# 使用 `PostgreSQL` 的 `LIMIT` 和 `OFFSET` 进行分页

将结果集分解成更小部分的技术称为分页。

假设你想在页面上显示产品列表；每页包含数量有限的产品。

以下语句返回包含前三个产品的第一页：

```sql
SELECT
  product_id,
  name,
  price
FROM
  products
ORDER BY
  price DESC
LIMIT 3;
```

输出：

```sql
 product_id |       name        |  price
------------+-------------------+---------
          9 | Galaxy Z Fold 6   | 2019.99
          1 | iPhone 16 Pro Max | 1649.00
          8 | OnePlus Open      | 1514.99
```

要在第二页显示接下来的三个产品，我们可以使用 `OFFSET` 跳过前三个产品，并使用 `LIMIT` 子句返回接下来的三个产品：

```sql
SELECT
  product_id,
  name,
  price
FROM
  products
ORDER BY
  price DESC
LIMIT 3 OFFSET 3;
```

输出：

```sql
 product_id |       name       |  price
------------+------------------+---------
          3 | Galaxy S24 Ultra | 1299.99
          4 | Galaxy S24 FE    |  949.99
          2 | iPhone 16        |  829.00
```

最后，使用 `LIMIT` 和 `OFFSET` 子句查询最后一页的产品：

```sql
SELECT
  product_id,
  name,
  price
FROM
  products
ORDER BY
  price DESC
LIMIT 3 OFFSET 3 * 2;
```

输出：

```sql
 product_id |    name     | price
------------+-------------+--------
          5 | Pixel 9 Pro | 799.00
          7 | OnePlus 12  | 745.00
          6 | Pixel 8a    | 399.00
```

# 总结

- 使用 `PostgreSQL` 的 `LIMIT` 子句来控制查询返回的行数。
- 使用 `OFFSET` 子句在返回部分行之前跳过一些行。
- `LIMIT` 和 `OFFSET` 有助于实现分页功能。
