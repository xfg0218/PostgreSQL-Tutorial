**摘要**：在本教程中，你将学习如何使用 `PostgreSQL` 关联子查询来选择依赖于外部查询值的数据。

# `PostgreSQL` 相关子查询入门

关联子查询是一种使用外部查询中的值的子查询。

与可以独立执行的常规子查询不同，`PostgreSQL` 可能必须为外部查询中的每一行执行一个关联子查询。

因此，为了提高查询性能，你应该尽可能避免使用关联子查询。

# `PostgreSQL` 关联子查询示例

假设我们有以下包含 `id` 、`name` 、`price` 和 `brand` 的 `products` 表：

创建产品表并向其中插入数据的脚本

```sql
CREATE TABLE products (
  id INT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  name VARCHAR(50) NOT NULL,
  price DEC(11, 2) NOT NULL CHECK (price > 0),
  brand VARCHAR(50) NOT NULL
);
INSERT INTO
  products (name, price, brand)
VALUES
  ('Galaxy S24', 799.99, 'Samsung'),
  ('iPhone 16', 1099.99, 'Apple'),
  ('iPhone 16 Pro Max', 1399.99, 'Apple'),
  ('iPhone 16 Plus', 1199.99, 'Apple'),
  ('Galaxy S24 Ultra', 1299.99, 'Samsung'),
  ('Galaxy S24 Plus', 1119.99, 'Samsung');
```

| id | name | price | brand |
|:----:|:------:|:-----:|:-----:|
| 1 | Galaxy S24 | 799.99 | Samsung |
| 2 | iPhone 16 | 1099.99 | Apple |
| 3 | iPhone 16 Pro Max | 1399.99 | Apple |
| 4 | iPhone 16 Plus | 1199.99 | Apple |
| 5 | Galaxy S24 Ultra | 1299.99 | Samsung |
| 6 | Galaxy S24 Plus | 1119.99 | Samsung |


以下示例展示了如何使用关联子查询获取每个品牌中最昂贵产品的产品名称、品牌和价格：

```sql
SELECT
  name,
  brand,
  price
FROM
  products p1
WHERE
  price = (
    SELECT
      MAX(price)
    FROM
      products p2
    WHERE
      p2.brand = p1.brand
  );
```

输出：

```sql
       name        |  brand  |  price
-------------------+---------+---------
 iPhone 16 Pro Max | Apple   | 1399.99
 Galaxy S24 Ultra  | Samsung | 1299.99
```

它的工作原理。

首先，外层查询从别名为 `p1` 的 `products` 表中选取 `name` 、`brand` 和 `price` ：

```sql
SELECT
  name,
  brand,
  price
FROM
  products p1
```

其次，`PostgreSQL` 会针对外部查询中的每一行执行相关子查询：

```sql
SELECT
  MAX(price)
FROM
  products p2
WHERE
  p2.brand = p1.brand
```

相关子查询从别名为 `p2` 的 `products` 表中选取最高价格，其中 `p2` 的品牌与外部查询中当前行的品牌 `（p1.brand）` 相匹配。

第三，外部查询中的 `WHERE` 子句会检查当前行的价格 `（p1.price）` 是否等于当前行所属品牌的最高价格。如果相等，`PostgreSQL` 就会将该行包含在结果集中。

详情如下：

`Row id 1` 

| id | name | price | brand |
|:---:|:---:|:---:|:---:|
| 1 |  Galaxy S24 |  799.99 |   Samsung |

子查询找到了三星品牌的最高价格（ `1299.99` ）。

`WHERE` 子句将价格（ `799.99` ）与最高价格（ `1299.99` ）进行比较。比较结果为 `false` 。`PostgreSQL` 不会将第1行包含在结果集中。

`Row id 2`

| id | name | price | brand |
|:---:|:---:|:---:|:---:|
| 2 |  iPhone 16 |  1099.99 |   Apple |

子查询找到了苹果品牌的最高价格（ `1399.99` ）。

`WHERE` 子句将价格（ `1099.99` ）与最高价格（ `1399.99` ）进行比较，结果返回 `false` 。因此，`PostgreSQL` 不会在结果集中包含行 `id 2` 。

`Row id 3`

| id | name | price | brand |
|:---:|:---:|:---:|:---:|
| 3 | 
iPhone 16 Pro Max |  1399.99 |   Apple |

子查询找到了苹果品牌的最高价格（ `1399.99` ）。

WHERE子句将价格（ `1399.99` ）与最高价格（ `1399.99` ）进行比较，结果返回`true` 。`PostgreSQL` 将行 `ID 3` 包含在结果集中。

`Row id 4`

| id | name | price | brand |
|:---:|:---:|:---:|:---:|
| 4 | iPhone 16 Plus | 1199.99 | Apple |

子查询找到了苹果品牌的最高价格（ `1399.99` ）。

`WHERE` 子句将价格（ `1199.99` ）与最高价格（ `1399.99` ）进行比较。比较结果为 `false` 。`PostgreSQL` 不会在结果集中包含行 `ID` 为 `4` 的记录。

`Row id 5`

| id | name | price | brand |
|:---:|:---:|:---:|:---:|
| 5 | Galaxy S24 Ultra | 1299.99 | Samsung |

子查询找到了三星品牌的最高价格（ `1299.99` ）。

`WHERE` 子句将价格（ `1299.99` ）与最高价格（ `1299.99` ）进行比较，结果为真。因此，`PostgreSQL` 不会在结果集中包含行 `ID 5` 。

`Row id 6`

| id | name | price | brand |
|:---:|:---:|:---:|:---:|
| 6 | Galaxy S24 Plus | 1119.99 | Samsung |

子查询找到了三星品牌的最高价格（ `1299.99` ）。

`WHERE` 子句将价格（ `1119.99` ）与最高价格（ `1299.99` ）进行比较，结果为 `false` 。`PostgreSQL` 不会在结果集中包含行 `id 6` 。

最终结果集如下：

| name | price | brand |
|:---:|:---:|:---:|
| iPhone 16 Pro Max | 1399.99 | Apple |
| Galaxy S24 Ultra | 1299.99 | Samsung |

这个示例表明，`PostgreSQL` 必须执行相关子查询，以找出外部查询中每一行的苹果品牌的最高价格。这既多余又低效。

# 使用连接重写相关子查询

以下示例使用带子查询的 `JOIN` 来加快查询速度：

```sql
SELECT
  p1.name,
  p1.brand,
  p1.price
FROM
  products p1
  JOIN (
    SELECT
      brand,
      MAX(price) AS max_price
    FROM
      products
    GROUP BY
      brand
  ) p2 ON p1.brand = p2.brand
  AND p1.price = p2.max_price;
```

它的工作原理。

首先，使用 `GROUP BY` 和 `MAX` 聚合函数按品牌筛选品牌和各品牌的最高产品价格：

```sql
SELECT
  brand,
  MAX(price) AS max_price
FROM
  products
GROUP BY
  brand;
```

其次，将 `products` 表与子查询返回的结果集在 `brand` 和 `price` 列上进行连接。这将检索出每个选定品牌中价格最高的产品。

这种方法减少了 `PostgreSQL` 执行子查询的次数，提高了查询性能，尤其是在`products` 表很大的情况下。

# 总结

- 关联子查询是依赖于外部查询的子查询。
