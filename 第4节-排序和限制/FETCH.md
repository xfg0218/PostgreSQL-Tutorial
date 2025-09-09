**摘要**: 在本教程中，你将学习如何使用 `PostgreSQL` 的 `FETCH` 子句从查询中检索部分行。

# `PostgreSQL FETCH` 简介

在 `PostgreSQL` 中，`OFFSET` 子句的作用类似于 `LIMIT` 子句。`FETCH` 子句允许你限制查询返回的行数。

`LIMIT` 子句并非 `SQL` 标准的一部分。不过，`FETCH` 子句是 `SQL:2008` 标准的一部分。如果您希望您的应用程序在未来支持其他数据库，请使用 `FETCH` 而非 `LIMIT` ，因为其他数据库供应商很可能会支持它。

以下是 `OFFSET FETCH` 子句的语法：

```sql
SELECT column1, column2
FROM table_name
ORDER BY sort_expression
OFFSET skip_count
FETCH { FIRST | NEXT } ] [row_count] {ROW | ROWS } ONLY;
```

在此语法中：

首先，在 `FROM` 子句中指定你想要检索数据的表的名称。

其次，在 `SELECT` 子句中限制最终结果集中要包含的行中的哪些列。

第三，使用 `ORDER BY` 子句按一列或多列中的值对行进行排序。

第四，在 `FETCH` 子句返回行的子集之前，在 `OFFSET` 子句中提供要跳过的行数：

```sql
OFFSET skip_count
```

`skip_count` 用于确定要跳过的行数。它可以是零或正整数。如果 `skip_count` 为零，查询将不会跳过任何行。

`OFFSET` 子句是可选的。如果省略该子句，查询也不会跳过任何行。

第五，在 `FETCH` 子句中设置要返回的行数 `(row_count)` 。

`row_count` 的默认值为 1，这意味着如果省略它，`FETCH` 将从查询中返回一行数据。

你可以交替使用 `FIRST` 和 `NEXT` 、`ROW` 以及 `ROWS` ，因为它们是同义词。

例如：

```sql
FETCH FIRST 1 ROW ONLY;
FETCH FIRST ROW ONLY;
FETCH FIRST 10 ROWS ONLY;
FETCH NEXT 1 ROW ONLY;
FETCH NEXT ROW ONLY;
FETCH NEXT 10 ROWS ONLY;
```

# 设置示例表

我们将使用在 `LIMIT` 教程中创建的 `products` 表来练习 `FETCH` 子句。

以下是创建 `products` 表并插入一些行的SQL脚本。

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

# 获取第一行

以下语句使用 `FETCH` 子句从 `products` 表中查询最昂贵的产品：

```sql
SELECT
  name,
  price
FROM
  products
ORDER BY
  price DESC
FETCH FIRST ROW ONLY;
```

输出：

```sql
      name       |  price
-----------------+---------
 Galaxy Z Fold 6 | 2019.99
```

# 获取一些行

以下示例使用 `FETCH` 子句从 `products` 表中检索价格最高的三种产品：

```sql
SELECT
  name,
  price
FROM
  products
ORDER BY
  price DESC
FETCH FIRST 3 ROWS ONLY;
```

输出：

```sql
       name        |  price
-------------------+---------
 Galaxy Z Fold 6   | 2019.99
 iPhone 16 Pro Max | 1649.00
 OnePlus Open      | 1514.99
```

# 在获取之前跳过一些行

以下语句使用 `OFFSET` 和 `FETCH` 子句从 `products` 表中获取第二贵的产品：

```sql
SELECT
  name,
  price
FROM
  products
ORDER BY
  price DESC
OFFSET 1
FETCH NEXT 1 ROW ONLY;
```

输出：

```sql
       name        |  price
-------------------+---------
 iPhone 16 Pro Max | 1649.00
```

# 使用 `PostgreSQL` 的 `FETCH` 子句进行分页

`FETCH` 子句有助于实现分页：

```sql
OFFSET record_per_page * (page_no - 1)
FETCH NEXT record_per_page ROWS ONLY.
```

在此语法中：

- `record_per_page` 是每页的记录数量。
- `page_no` 是页码，例如 `1、2、3` 。

假设你希望每页显示3个产品 `(record_per_page)` ，并想获取第2页的产品 `(page_no)`，你可以使用以下查询：

```sql
SELECT
  name,
  price
FROM
  products
ORDER BY
  price DESC
OFFSET 3 * (2 - 1)
FETCH NEXT 3 ROW ONLY;
```

输出：

```sql
       name       |  price
------------------+---------
 Galaxy S24 Ultra | 1299.99
 Galaxy S24 FE    |  949.99
 iPhone 16        |  829.00
```

# 总结

- 使用 `PostgreSQL` 的 `FETCH` 从查询中返回部分行。
- 使用 `OFFSET FETCH` 在返回部分行之前跳过一些行。
